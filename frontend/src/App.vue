<template>
  <div class="hiphop-app" :class="{ 'night-mode': nightMode }">
    <!-- Header with mode toggle -->
    <header>
      <h1>THE "KEEPING IT REAL" PARADOX</h1>
      <p>Exploring Authenticity in Hip-Hop Through Interactive Graffiti</p>
      <button @click="toggleMode" class="mode-toggle">
        {{ nightMode ? 'DAY MODE' : 'NIGHT MODE' }}
      </button>
    </header>

    <!-- Main interactive canvas -->
    <div class="graffiti-wall" ref="wall" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @mouseleave="stopDrawing">
      <!-- Wall texture background -->
      <div class="wall-texture"></div>
      
      <!-- Graffiti tags will be dynamically added here -->
      <div v-for="(tag, index) in tags" :key="index" 
           :class="['graffiti-tag', tag.type]" 
           :style="{ 
             left: tag.x + 'px', 
             top: tag.y + 'px',
             transform: `rotate(${tag.rotation}deg) scale(${tag.scale})`,
             opacity: tag.opacity,
             color: tag.color
           }"
           @click="explainTag(tag)">
        {{ tag.text }}
      </div>
      
      <!-- Philosophy concept bubbles -->
      <div v-for="(concept, index) in concepts" :key="'concept-'+index"
           :class="['philosophy-bubble', concept.type]"
           :style="{ left: concept.x + 'px', top: concept.y + 'px' }"
           @click="explainConcept(concept)">
        {{ concept.name }}
      </div>
    </div>

    <!-- Spray can selector -->
    <div class="spray-can-selector">
      <div v-for="(can, index) in sprayCans" :key="index" 
           :class="['spray-can', { 'active': activeCan === index }]"
           :style="{ backgroundColor: can.color }"
           @click="selectCan(index)">
        <span class="can-label">{{ can.label }}</span>
      </div>
    </div>

    <!-- Info panel -->
    <div class="info-panel" :class="{ 'visible': showInfoPanel }">
      <button class="close-panel" @click="showInfoPanel = false">×</button>
      <h2>{{ activeInfo.title }}</h2>
      <p>{{ activeInfo.content }}</p>
      <div class="example" v-if="activeInfo.example">
        <h3>Hip-Hop Example:</h3>
        <p>{{ activeInfo.example }}</p>
      </div>
    </div>

    <!-- Audio element for spray sound -->
    <audio ref="spraySound" src="https://assets.codepen.io/3364143/spray-sound.mp3" preload="auto"></audio>
  </div>
</template>

<script>
export default {
  name: 'HipHopPersonaParadox',
  data() {
    return {
      nightMode: true,
      isDrawing: false,
      activeCan: 0,
      tags: [],
      concepts: [],
      showInfoPanel: false,
      activeInfo: {
        title: '',
        content: '',
        example: ''
      },
      sprayCans: [
        { label: 'EGO', color: '#ff3366', type: 'ego' },
        { label: 'ALTER EGO', color: '#33ffcc', type: 'alter-ego' },
        { label: 'PERSONA', color: '#ffcc33', type: 'persona' },
        { label: 'TRUTH', color: '#ffffff', type: 'truth' },
        { label: 'STREET', color: '#3366ff', type: 'street' }
      ],
      philosophy: [
        {
          name: 'Hyperreality',
          content: 'Baudrillard\'s concept where the representation becomes more "real" than reality itself. In hip-hop, the crafted persona often feels more authentic than the artist\'s actual life.',
          example: '50 Cent\'s "bulletproof" image after surviving 9 shots - the myth became larger than the man.'
        },
        {
          name: 'Bad Faith',
          content: 'Sartre\'s idea of denying one\'s freedom by conforming to external expectations. Rappers may adopt personas that fit industry standards rather than their true selves.',
          example: 'Will Smith transitioning from "Fresh Prince" to harder rap personas to gain street credibility.'
        },
        {
          name: 'Will to Power',
          content: 'Nietzsche\'s concept of creating your own values. Hip-hop artists redefine "realness" on their own terms.',
          example: 'Kanye West rejecting gangsta rap norms to create his own version of authenticity.'
        },
        {
          name: 'Commodification',
          content: 'Marxist critique of how capitalism turns authenticity into a sellable product. "Realness" becomes a marketable trait.',
          example: 'White suburban teens adopting "gangsta" personas because it sells records.'
        }
      ]
    };
  },
  mounted() {
    this.createPhilosophyBubbles();
    // Pre-create some tags to make the wall look lived-in
    this.tags = [
      { text: 'REAL', x: 100, y: 80, type: 'street', rotation: -5, scale: 1.2, opacity: 0.9, color: '#3366ff' },
      { text: 'FAKE?', x: 300, y: 120, type: 'truth', rotation: 3, scale: 1, opacity: 0.8, color: '#ffffff' },
      { text: 'KEEP IT 100', x: 500, y: 200, type: 'persona', rotation: -2, scale: 1.1, opacity: 0.85, color: '#ffcc33' },
      { text: 'WHO AM I?', x: 200, y: 300, type: 'ego', rotation: 5, scale: 0.9, opacity: 0.7, color: '#ff3366' }
    ];
  },
  methods: {
    toggleMode() {
      this.nightMode = !this.nightMode;
    },
    selectCan(index) {
      this.activeCan = index;
    },
    startDrawing(e) {
      this.isDrawing = true;
      this.draw(e);
      this.playSpraySound();
    },
    stopDrawing() {
      this.isDrawing = false;
    },
    draw(e) {
      if (!this.isDrawing) return;
      
      const wall = this.$refs.wall;
      const rect = wall.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      
      // Only add new tag if far enough from last one
      if (this.tags.length === 0 || 
          Math.abs(x - this.tags[this.tags.length - 1].x) > 30 || 
          Math.abs(y - this.tags[this.tags.length - 1].y) > 30) {
        
        const sprayCan = this.sprayCans[this.activeCan];
        
        this.tags.push({
          text: this.getRandomTagText(sprayCan.type),
          x: x,
          y: y,
          type: sprayCan.type,
          rotation: Math.random() * 20 - 10, // -10 to 10 degrees
          scale: 0.7 + Math.random() * 0.6, // 0.7 to 1.3
          opacity: 0.7 + Math.random() * 0.3, // 0.7 to 1.0
          color: sprayCan.color
        });
        
        // Limit number of tags to prevent performance issues
        if (this.tags.length > 50) {
          this.tags.shift();
        }
      }
    },
    playSpraySound() {
      const sound = this.$refs.spraySound;
      sound.currentTime = 0;
      sound.play();
    },
    getRandomTagText(type) {
      const texts = {
        'ego': ['I AM', 'ME', 'SELF', 'SOUL', 'MYSELF'],
        'alter-ego': ['SLIM SHADY', 'GUNNA', 'MF DOOM', 'GHOSTFACE', 'IGOR'],
        'persona': ['IMAGE', 'MASK', 'ROLE', 'CHARACTER', 'FRONT'],
        'truth': ['REAL', 'FACT', 'RAW', 'HONEST', 'TRUE'],
        'street': ['GRIND', 'STRUGGLE', 'HOOD', 'BLOCK', 'PAIN']
      };
      const options = texts[type] || ['REAL', 'FAKE', 'WHO?', 'WHY?'];
      return options[Math.floor(Math.random() * options.length)];
    },
    createPhilosophyBubbles() {
      const wall = this.$refs.wall;
      if (!wall) return;
      
      const rect = wall.getBoundingClientRect();
      const wallWidth = rect.width;
      const wallHeight = rect.height;
      
      this.concepts = this.philosophy.map((concept, index) => {
        return {
          name: concept.name,
          x: (wallWidth * 0.8) * (index / this.philosophy.length) + (wallWidth * 0.1),
          y: wallHeight * 0.7, 
          type: `philosophy-${index % 4}`
        };
      });
    },
    explainTag(tag) {
      const explanations = {
        'ego': {
          title: 'THE EGO',
          content: 'The core self before any persona is applied. In hip-hop, this is the artist before fame, before crafting an image.',
          example: 'Before becoming "Snoop Dogg," he was Calvin Broadus - the ego beneath the persona.'
        },
        'alter-ego': {
          title: 'ALTER EGO',
          content: 'A secondary self, often more extreme than the original. Hip-hop artists create alter egos to express different aspects of their identity.',
          example: 'Eminem\'s "Slim Shady" allowed Marshall Mathers to express darker thoughts he couldn\'t as himself.'
        },
        'persona': {
          title: 'PERSONA',
          content: 'The crafted image presented to the world. In hip-hop, personas often amplify certain traits to meet audience expectations.',
          example: 'Nicki Minaj\'s various personas (Roman Zolanski, Harajuku Barbie) let her explore different styles.'
        },
        'truth': {
          title: 'TRUTH',
          content: 'The tension between authenticity and performance. Fans demand "truth" but often reject it when it doesn\'t match their expectations.',
          example: 'When Jay-Z revealed his drug dealer past wasn\'t as extensive as his lyrics suggested, some fans called him "fake."'
        },
        'street': {
          title: 'STREET CRED',
          content: 'The currency of authenticity in hip-hop. Even artists from middle-class backgrounds feel pressure to prove "street" legitimacy.',
          example: 'Drake\'s constant references to his Toronto upbringing to maintain credibility despite his acting career.'
        }
      };
      
      this.activeInfo = explanations[tag.type] || {
        title: 'GRAFFITI TAG',
        content: 'Each spray represents a facet of hip-hop identity. Click on philosophy bubbles below to explore deeper concepts.'
      };
      this.showInfoPanel = true;
    },
    explainConcept(concept) {
      const foundConcept = this.philosophy.find(c => c.name === concept.name);
      if (foundConcept) {
        this.activeInfo = {
          title: foundConcept.name,
          content: foundConcept.content,
          example: foundConcept.example
        };
        this.showInfoPanel = true;
      }
    }
  }
};
</script>

<style scoped>
.hiphop-app {
  font-family: 'Arial Black', sans-serif;
  background-color: #111;
  color: #fff;
  min-height: 100vh;
  padding: 20px;
  transition: background-color 0.5s, color 0.5s;
}

.hiphop-app.night-mode {
  background-color: #111;
  color: #fff;
}

.hiphop-app:not(.night-mode) {
  background-color: #f5f5f5;
  color: #333;
}

header {
  text-align: center;
  margin-bottom: 30px;
}

header h1 {
  font-size: 3rem;
  margin: 0;
  text-transform: uppercase;
  letter-spacing: 3px;
  color: #ff3366;
}

header p {
  font-size: 1.2rem;
  margin-top: 10px;
  opacity: 0.8;
}

.mode-toggle {
  background: #333;
  color: white;
  border: 2px solid #ff3366;
  padding: 8px 15px;
  font-weight: bold;
  cursor: pointer;
  margin-top: 15px;
  transition: all 0.3s;
}

.mode-toggle:hover {
  background: #ff3366;
}

.graffiti-wall {
  position: relative;
  width: 90%;
  height: 500px;
  margin: 0 auto;
  background-color: #222;
  border: 10px solid #444;
  box-shadow: 0 0 30px rgba(0,0,0,0.5);
  overflow: hidden;
  cursor: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10" fill="none" stroke="%23ffffff" stroke-width="2"/></svg>') 12 12, auto;
}

.wall-texture {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    linear-gradient(rgba(0,0,0,0.2) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0,0,0,0.2) 1px, transparent 1px);
  background-size: 20px 20px;
}

.graffiti-tag {
  position: absolute;
  font-size: 24px;
  font-weight: bold;
  text-transform: uppercase;
  text-shadow: 2px 2px 0 rgba(0,0,0,0.5);
  cursor: pointer;
  transition: transform 0.3s, opacity 0.3s;
}

.graffiti-tag:hover {
  transform: scale(1.1) !important;
  opacity: 1 !important;
  z-index: 10;
}

.philosophy-bubble {
  position: absolute;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  font-weight: bold;
  font-size: 10px;
  cursor: pointer;
  animation: float 3s infinite ease-in-out;
  border: 3px solid white;
  background: rgba(255,255,255,0.1);
  transition: all 0.3s;
  min-height: 100px;
}

.philosophy-bubble:hover {
  transform: scale(1.1);
  background: rgba(255,255,255,0.2);
}

.philosophy-0 { animation-delay: 0s; top: 20%; }
.philosophy-1 { animation-delay: 0.5s; top: 30%; }
.philosophy-2 { animation-delay: 1s; top: 40%; }
.philosophy-3 { animation-delay: 1.5s; top: 50%; }

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-20px); }
}

.spray-can-selector {
  display: flex;
  justify-content: center;
  margin: 30px auto;
  gap: 15px;
}

.spray-can {
  width: 80px;
  height: 120px;
  border-radius: 5px 5px 20px 20px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 10px;
  cursor: pointer;
  position: relative;
  transition: transform 0.3s;
  border: 2px solid transparent;
}

.spray-can::before {
  content: '';
  position: absolute;
  top: -15px;
  left: 30px;
  width: 20px;
  height: 30px;
  background: inherit;
  border-radius: 5px 5px 0 0;
}

.spray-can:hover {
  transform: translateY(-10px);
}

.spray-can.active {
  transform: translateY(-20px);
  border-color: white;
  box-shadow: 0 0 20px rgba(255,255,255,0.5);
}

.can-label {
  writing-mode: vertical-rl;
  transform: rotate(180deg);
  font-weight: bold;
  color: black;
  text-shadow: 1px 1px 0 rgba(255,255,255,0.5);
}

.info-panel {
  position: fixed;
  bottom: -300px;
  left: 50%;
  transform: translateX(-50%);
  width: 80%;
  max-width: 600px;
  background: rgba(0,0,0,0.9);
  border: 3px solid #ff3366;
  padding: 20px;
  border-radius: 10px 10px 0 0;
  transition: bottom 0.5s;
  z-index: 100;
}

.info-panel.visible {
  bottom: 0;
}

.close-panel {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
}

.info-panel h2 {
  color: #ff3366;
  margin-top: 0;
}

.info-panel h3 {
  color: #33ffcc;
}

.example {
  margin-top: 15px;
  padding-top: 15px;
  border-top: 1px solid #444;
}
</style>
