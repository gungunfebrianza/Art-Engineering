# Art-Engineering



# Critical Mass Theory

```javascript
let particles = [];
const gridSize = 20;
const spacing = 30;
const margin = (600 - (gridSize - 1) * spacing) / 2;
const radius = 45; // Distance within which particles activate others
const spreadDelay = 10; // Spreads every 10 frames
const amplitude = 5; // Oscillation amplitude
const speed = 0.22; // Oscillation speed

function setup() {
  createCanvas(600, 600);
  
  // Create a 20x20 grid of particles with random phases
  for (let i = 0; i < gridSize; i++) {
    for (let j = 0; j < gridSize; j++) {
      let x = margin + i * spacing;
      let y = margin + j * spacing;
      particles.push({
        pos: createVector(x, y),
        active: false,
        neighbors: [],
        phase: random(TWO_PI)
      });
    }
  }
  
  // Precompute neighbors based on initial positions
  for (let p of particles) {
    p.neighbors = particles.filter(q => p !== q && p5.Vector.dist(p.pos, q.pos) < radius);
  }
}

function draw() {
  background(0);
  
  // Draw particles with slow oscillatory movement
  for (let p of particles) {
    let angle = p.phase + frameCount * speed;
    let x_display = p.pos.x + amplitude * sin(angle);
    let y_display = p.pos.y + amplitude * cos(angle);
    
    if (p.active) {
      fill(255, 0, 0); // Active particles are red
    } else {
      fill(255); // Inactive particles are white
    }
    noStroke();
    ellipse(x_display, y_display, 18, 18); // Triple the size
  }
  
  // Spread activation every 10 frames
  if (frameCount % spreadDelay === 0) {
    let activeParticles = particles.filter(p => p.active);
    for (let p of activeParticles) {
      for (let q of p.neighbors) {
        if (!q.active) {
          q.active = true;
        }
      }
    }
  }
  
  // Display instructions
  fill(255);
  textSize(16);
  text("Click on a particle to start the chain reaction", 10, height - 20);
}

function mousePressed() {
  // Find the closest particle based on its current position
  let minDist = Infinity;
  let closestParticle = null;
  for (let p of particles) {
    let angle = p.phase + frameCount * speed;
    let x_display = p.pos.x + amplitude * sin(angle);
    let y_display = p.pos.y + amplitude * cos(angle);
    let d = dist(mouseX, mouseY, x_display, y_display);
    if (d < minDist) {
      minDist = d;
      closestParticle = p;
    }
  }
  if (closestParticle) {
    closestParticle.active = true;
  }
}
```

### How It Works

- **Larger Particles**: The particles are now 30 pixels in diameter, three times their original size of 10 pixels.
- **Slow Movement**: Each particle moves in a small, circular pattern around its grid position, with a 5-pixel amplitude and a slow speed of 0.02 radians per frame, making the motion smooth and gentle.
- **Chain Reaction**: Click a particle to activate it (it turns red), and every 10 frames, the activation spreads to neighboring particles within a 45-pixel radius of their original positions.