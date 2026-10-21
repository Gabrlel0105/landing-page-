<template>
  <canvas ref="canvasRef" class="shooting-stars-canvas"></canvas>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const canvasRef = ref(null);
let ctx;
let animationFrameId;

let stars = [];
let particles = [];

const MAX_STARS = 4;

function random(min, max) {
  return Math.random() * (max - min) + min;
}

function spawnStar(width, height) {
  const edge = Math.floor(Math.random() * 4);
  let x, y, vx, vy;
  
  const speedOptions = [2.5, 4.5, 7];
  const speed = speedOptions[Math.floor(Math.random() * speedOptions.length)];
  
  if (edge === 0) { // top
    x = random(0, width);
    y = 0;
    vx = random(-speed, speed);
    vy = random(speed / 2, speed);
  } else if (edge === 1) { // right
    x = width;
    y = random(0, height);
    vx = random(-speed, -speed / 2);
    vy = random(-speed, speed);
  } else if (edge === 2) { // bottom
    x = random(0, width);
    y = height;
    vx = random(-speed, speed);
    vy = random(-speed, -speed / 2);
  } else { // left
    x = 0;
    y = random(0, height);
    vx = random(speed / 2, speed);
    vy = random(-speed, speed);
  }
  
  return {
    x, y,
    vx, vy,
    life: 0,
    explodeTime: random(50, 160), // frames antes de explotar
    history: [] // para la estela
  };
}

function spawnParticles(x, y, scale = 1) {
  const numParticles = Math.floor(random(30 * scale, 60 * scale));
  const colors = ['#ffeb3b', '#ff9800', '#f44336', '#00bcd4', '#4caf50', '#e91e63', '#ffffff', '#64ffda'];
  const singleColor = colors[Math.floor(Math.random() * colors.length)];
  const isGiant = scale > 1;
  
  for (let i = 0; i < numParticles; i++) {
    const angle = random(0, Math.PI * 2);
    const speed = random(1 * scale, 7 * scale);
    particles.push({
      x, y,
      vx: Math.cos(angle) * speed,
      vy: Math.sin(angle) * speed,
      alpha: 1,
      color: isGiant ? colors[Math.floor(Math.random() * colors.length)] : singleColor,
      decay: random(0.015 / scale, 0.035 / scale) // Partículas más grandes duran más tiempo
    });
  }
}

function resizeCanvas() {
  if (canvasRef.value) {
    const parent = canvasRef.value.parentElement;
    canvasRef.value.width = parent ? parent.clientWidth : window.innerWidth;
    canvasRef.value.height = parent ? parent.clientHeight : window.innerHeight;
  }
}

function update() {
  if (!canvasRef.value) return;
  const width = canvasRef.value.width;
  const height = canvasRef.value.height;
  
  ctx.clearRect(0, 0, width, height);
  
  if (stars.length < MAX_STARS && Math.random() < 0.02) {
    stars.push(spawnStar(width, height));
  }
  
  for (let i = 0; i < stars.length; i++) {
    if (stars[i].toRemove) continue;
    for (let j = i + 1; j < stars.length; j++) {
      if (stars[j].toRemove) continue;
      
      let s1 = stars[i];
      let s2 = stars[j];
      let dist = Math.hypot(s1.x - s2.x, s1.y - s2.y);
      
      if (dist < 40) {
        let midX = (s1.x + s2.x) / 2;
        let midY = (s1.y + s2.y) / 2;
        
        spawnParticles(midX, midY, 2.5);
        
        s1.toRemove = true;
        s2.toRemove = true;
      }
    }
  }
  
  for (let i = stars.length - 1; i >= 0; i--) {
    let s = stars[i];
    
    if (s.toRemove) {
      stars.splice(i, 1);
      continue;
    }
    
    s.history.push({x: s.x, y: s.y});
    if (s.history.length > 15) s.history.shift(); // longitud de la estela
    
    s.x += s.vx;
    s.y += s.vy;
    s.life++;
    
    // Dibujar estela
    if (s.history.length > 1) {
      ctx.beginPath();
      ctx.moveTo(s.history[0].x, s.history[0].y);
      for (let j = 1; j < s.history.length; j++) {
        ctx.lineTo(s.history[j].x, s.history[j].y);
      }
      
      // Crear gradiente para que la cola se desvanezca
      const grad = ctx.createLinearGradient(
        s.history[0].x, s.history[0].y,
        s.x, s.y
      );
      grad.addColorStop(0, 'rgba(255, 255, 255, 0)');
      grad.addColorStop(1, 'rgba(255, 255, 255, 0.8)');
      
      ctx.strokeStyle = grad;
      ctx.lineWidth = 2;
      ctx.lineCap = 'round';
      ctx.stroke();
    }
    
    // Dibujar cabeza de la estrella (forma de estrella de 4 puntas que gira)
    const spikes = 4;
    const outerRadius = 4.5;
    const innerRadius = 1.5;
    let rot = Math.PI / 2 * 3 + (s.life * 0.15); // Rota según su tiempo de vida
    let step = Math.PI / spikes;

    ctx.beginPath();
    for (let k = 0; k < spikes; k++) {
      ctx.lineTo(s.x + Math.cos(rot) * outerRadius, s.y + Math.sin(rot) * outerRadius);
      rot += step;
      ctx.lineTo(s.x + Math.cos(rot) * innerRadius, s.y + Math.sin(rot) * innerRadius);
      rot += step;
    }
    ctx.closePath();
    ctx.fillStyle = '#ffffff';
    ctx.fill();
    
    // Comprobar tiempo de explosión o si sale de los límites
    const outOfBounds = s.x < 0 || s.x > width || s.y < 0 || s.y > height;
    
    if (s.life >= s.explodeTime || outOfBounds) {
      // Explotar sólo si alcanzó el tiempo (no si simplemente salió de la pantalla)
      if (s.life >= s.explodeTime && !outOfBounds) {
        spawnParticles(s.x, s.y, 0.35); // Explosión normal aún más chica (35% del original)
      }
      stars.splice(i, 1);
    }
  }
  
  // Actualizar y dibujar partículas
  for (let i = particles.length - 1; i >= 0; i--) {
    let p = particles[i];
    p.x += p.vx;
    p.y += p.vy;
    p.vy += 0.05; // gravedad ligera
    p.alpha -= p.decay; // desvanecimiento
    
    if (p.alpha <= 0) {
      particles.splice(i, 1);
      continue;
    }
    
    ctx.beginPath();
    ctx.arc(p.x, p.y, 2, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(${hexToRgb(p.color)}, ${p.alpha})`;
    ctx.fill();
  }
  
  animationFrameId = requestAnimationFrame(update);
}

function hexToRgb(hex) {
  let c;
  if(/^#([A-Fa-f0-9]{3}){1,2}$/.test(hex)){
      c = hex.substring(1).split('');
      if(c.length == 3){
          c = [c[0], c[0], c[1], c[1], c[2], c[2]];
      }
      c = '0x' + c.join('');
      return [(c>>16)&255, (c>>8)&255, c&255].join(',');
  }
  return '255,255,255';
}

onMounted(() => {
  ctx = canvasRef.value.getContext('2d');
  resizeCanvas();
  window.addEventListener('resize', resizeCanvas);
  update();
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', resizeCanvas);
  cancelAnimationFrame(animationFrameId);
});
</script>

<style scoped>
.shooting-stars-canvas {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none; /* Permite hacer clic a través del canvas */
  z-index: 1;
}
</style>
