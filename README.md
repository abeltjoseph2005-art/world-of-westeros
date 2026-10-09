<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>THE WORLD OF WESTEROS — Royalty, Lore & War Simulator</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            obsidian: '#0a0b0e',
            charcoal: '#12151c',
            steel: '#1e2330',
            parchment: '#c8b693',
            'parchment-dark': '#8c7b5b',
            gold: '#d4af37',
            'gold-bright': '#f3e5ab',
            targaryen: '#8b0000',
            stark: '#4a6572',
            lannister: '#800020',
            tyrell: '#2d5a27',
            arryn: '#1f4e79',
          },
          fontFamily: {
            cinzel: ['Cinzel', 'serif'],
            medieval: ['Cinzel Decorative', 'serif'],
            sans: ['Inter', 'sans-serif']
          }
        }
      }
    }
  </script>

  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Cinzel+Decorative:wght@700;900&family=Cinzel:wght@400;600;700;900&family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

  <!-- React & Babel -->
  <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>

  <!-- Three.js for 3D visual FX -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

  <style>
    body {
      background-color: #0a0b0e;
      color: #c8b693;
      font-family: 'Cinzel', serif;
      overflow-x: hidden;
    }

    /* Custom Scrollbars */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #0a0b0e;
    }
    ::-webkit-scrollbar-thumb {
      background: #332a1b;
      border-radius: 3px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #d4af37;
    }

    /* Atmospheric Animations */
    .bg-embers {
      background-image: radial-gradient(#d4af37 1px, transparent 1px), radial-gradient(#8b0000 1px, transparent 1px);
      background-size: 40px 40px;
      background-position: 0 0, 20px 20px;
      animation: emberMove 20s linear infinite;
    }

    @keyframes emberMove {
      0% { background-position: 0 0, 20px 20px; }
      100% { background-position: 0 -400px, 20px -380px; }
    }

    .border-gold-engraved {
      border: 2px solid #d4af37;
      box-shadow: inset 0 0 10px rgba(212, 175, 55, 0.2), 0 0 15px rgba(0,0,0,0.8);
    }

    .gold-gradient-text {
      background: linear-gradient(135deg, #fff2a3 0%, #d4af37 50%, #aa7c11 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  </style>
</head>
<body class="selection:bg-gold selection:text-obsidian">
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect, useRef, useMemo } = React;

    // --- COMPREHENSIVE WESTEROS LORE DATABASE ---
    const LORE_DATABASE = {
      eras: [
        { id: 'before_conquest', name: 'Before the Conquest', period: '12,000 BC - 2 BC' },
        { id: 'aegons_conquest', name: "Aegon's Conquest", period: '2 BC - 1 AC' },
        { id: 'targaryen_dynasty', name: 'Targaryen Dynasty', period: '1 AC - 129 AC' },
        { id: 'dance_of_dragons', name: 'Dance of the Dragons', period: '129 AC - 131 AC' },
        { id: 'later_targaryens', name: 'Later Targaryen Reign', period: '131 AC - 281 AC' },
        { id: 'roberts_rebellion', name: "Robert's Rebellion", period: '281 AC - 283 AC' },
        { id: 'war_five_kings', name: 'War of the Five Kings', period: '298 AC - 303 AC' },
        { id: 'fall_dragon_queen', name: 'Fall of the Dragon Queen', period: '304 AC - 305 AC' }
      ],

      houses: [
        {
          id: 'targaryen',
          name: 'House Targaryen',
          words: 'Fire and Blood',
          sigil: 'Three-headed dragon, red on black',
          seat: 'Dragonstone / King\'s Landing',
          region: 'Crownlands',
          color: '#8b0000',
          accent: '#d4af37',
          military: '10,000 Unsullied, 100,000 Dothraki, 3 Royal Dragons (Apex Era)',
          founding: 'Valyrian Freehold dragonlords who escaped the Doom to Dragonstone in 114 BC.',
          history: 'United the Seven Kingdoms under King Aegon I through dragonfire and diplomatic submission. Ruled for nearly three centuries until Aerys II was overthrown.',
          tree: [
            { name: "Aegon I (The Conqueror)", spouse: "Visenya & Rhaenys", claim: "Founder & First King of Seven Kingdoms" },
            { name: "Viserys I", spouse: "Aemma Arryn / Alicent Hightower", claim: "Fifth King of the Iron Throne" },
            { name: "Rhaenyra Targaryen", spouse: "Daemon Targaryen", claim: "First Queen Regent (Disputed / The Blacks)" },
            { name: "Aegon II Targaryen", spouse: "Helaena Targaryen", claim: "Sixth Monarch (The Greens)" },
            { name: "Daenerys Stormborn", spouse: "Khal Drogo / Hizdahr zo Loraq", claim: "The Breaker of Chains, Mother of Dragons" },
            { name: "Aegon Targaryen (Jon Snow)", spouse: "None", claim: "Rightful son of Rhaegar Targaryen and Lyanna Stark" }
          ]
        },
        {
          id: 'stark',
          name: 'House Stark',
          words: 'Winter Is Coming',
          sigil: 'Grey direwolf on an ice-white field',
          seat: 'Winterfell',
          region: 'The North',
          color: '#4a6572',
          accent: '#ffffff',
          military: '20,000 Northern Foot, Heavy Cavalry, Crannogmen skirmishers',
          founding: 'Founded by Bran the Builder during the Age of Heroes 8,000 years ago.',
          history: 'Kings of Winter who bent the knee to Aegon the Conqueror (Torrhen Stark). Known for unyielding honor and ruling the massive, harsh North.',
          tree: [
            { name: "Torrhen Stark", spouse: "Unknown", claim: "The King Who Knelt" },
            { name: "Rickard Stark", spouse: "Lyarra Stark", claim: "Warden of the North (Executed by Mad King)" },
            { name: "Eddard 'Ned' Stark", spouse: "Catelyn Tully", claim: "Hand of the King & Warden of the North" },
            { name: "Robb Stark", spouse: "Talisa Maegyr", claim: "The Young Wolf, King in the North" },
            { name: "Sansa Stark", spouse: "Ramsay Bolton (Forced)", claim: "Queen in the North" }
          ]
        },
        {
          id: 'lannister',
          name: 'House Lannister',
          words: 'Hear Me Roar!',
          sigil: 'Golden lion rampant on a crimson field',
          seat: 'Casterly Rock',
          region: 'The Westerlands',
          color: '#800020',
          accent: '#ffd700',
          military: '35,000 Well-drilled heavy infantry, heavy horse, Casterly Rock reserves',
          founding: 'Descended from Lann the Clever who tricked the Casterlys out of their Rock during the Age of Heroes.',
          history: 'The wealthiest house in Westeros due to the deep gold mines under the Rock. Rebuilt into an ruthless power by Lord Tywin Lannister.',
          tree: [
            { name: "Tywin Lannister", spouse: "Joanna Lannister", claim: "Hand of the King & Lord of Casterly Rock" },
            { name: "Cersei Lannister", spouse: "Robert Baratheon", claim: "First Queen Regnant of the Seven Kingdoms" },
            { name: "Jaime Lannister", spouse: "None", claim: "Lord Commander of the Kingsguard / The Kingslayer" },
            { name: "Tyrion Lannister", spouse: "Sansa Stark (Unfulfilled)", claim: "Hand to Daenerys & Bran the Broken" },
            { name: "Joffrey Baratheon/Lannister", spouse: "Margaery Tyrell", claim: "King on the Iron Throne (Bastard of Incest)" }
          ]
        },
        {
          id: 'baratheon',
          name: 'House Baratheon',
          words: 'Ours Is the Fury',
          sigil: 'Crowned black stag on a gold field',
          seat: 'Storm\'s End / King\'s Landing',
          region: 'The Stormlands',
          color: '#3a2d12',
          accent: '#d4af37',
          military: '15,000 Stormland Knights and shock infantry',
          founding: 'Founded by Orys Baratheon, Aegon the Conqueror\'s bastard half-brother who took Argilac\'s castle.',
          history: 'Fierce warriors who led the rebellion that tore down the 300-year Targaryen Dynasty under Robert Baratheon.',
          tree: [
            { name: "Orys Baratheon", spouse: "Argella Durrandon", claim: "First Hand of the King" },
            { name: "Robert Baratheon", spouse: "Cersei Lannister", claim: "Demon of the Trident, Monarch of the Seven Kingdoms" },
            { name: "Stannis Baratheon", spouse: "Selyse Florent", claim: "Lord of Dragonstone & Rightful King" },
            { name: "Renly Baratheon", spouse: "Margaery Tyrell", claim: "Lord of Storm's End" }
          ]
        }
      ],

      dragons: [
        {
          id: 'balerion',
          name: 'Balerion the Black Dread',
          rider: 'Aegon I Targaryen, Maegor I, Viserys I',
          scaleColor: '#121212',
          flameColor: '#000000',
          lengthMetres: 120,
          wingspanMetres: 150,
          status: 'Deceased (Natural old age in 94 AC)',
          history: 'The largest dragon in Targaryen history. His jaw could swallow an aurochs whole. His flame forged the Iron Throne and melted Harrenhal.'
        },
        {
          id: 'vhagar',
          name: 'Vhagar',
          rider: 'Queen Visenya, Laena Velaryon, Aemond Targaryen',
          scaleColor: '#2b3e2b',
          flameColor: '#ff6600',
          lengthMetres: 95,
          wingspanMetres: 120,
          status: 'Killed at the Battle Above the Gods Eye (130 AC)',
          history: 'Second only to Balerion in size. Her roar shook the foundations of Storm\'s End. The decisive apex weapon for the Greens during the Dance of the Dragons.'
        },
        {
          id: 'drogon',
          name: 'Drogon',
          rider: 'Daenerys Targaryen',
          scaleColor: '#1a1a1a',
          flameColor: '#cc0000',
          lengthMetres: 60,
          wingspanMetres: 75,
          status: 'Alive (Fled East toward Valyria)',
          history: 'Hatched in the funeral pyre of Khal Drogo. Named after Drogo. Reincarnation of Balerion\'s terror, destroyed the Iron Fleet and King\'s Landing.'
        },
        {
          id: 'caraxes',
          name: 'Caraxes (The Blood Wyrm)',
          rider: 'Daemon Targaryen',
          scaleColor: '#8b0000',
          flameColor: '#ff2200',
          lengthMetres: 70,
          wingspanMetres: 80,
          status: 'Killed in dual death with Vhagar at Gods Eye',
          history: 'Fierce, serpentine, and battle-hardened dragon with a high-pitched screech. Feared across the Stepstones.'
        }
      ],

      mapLocations: [
        { id: 'kl', name: "King's Landing", house: 'Crownlands / Targaryen', coords: { x: 62, y: 58 }, desc: "Capital of the Seven Kingdoms, site of the Iron Throne and the Red Keep." },
        { id: 'wf', name: 'Winterfell', house: 'House Stark', coords: { x: 40, y: 25 }, desc: "Ancient seat of House Stark, built with thick double walls and heated by hot springs." },
        { id: 'cr', name: 'Casterly Rock', house: 'House Lannister', coords: { x: 22, y: 52 }, desc: "Colossal fortress carved into a gold-rich mountain towering over the Sunset Sea." },
        { id: 'ds', name: 'Dragonstone', house: 'House Targaryen', coords: { x: 74, y: 52 }, desc: "Volcanic island fortress constructed with advanced Valyrian stone-shaping craft." },
        { id: 'wall', name: 'The Wall', house: 'Night\'s Watch', coords: { x: 48, y: 12 }, desc: "700-foot colossal barrier of solid ice stretching 300 miles across the northern border." },
        { id: 'hg', name: 'Highgarden', house: 'House Tyrell', coords: { x: 30, y: 72 }, desc: "Lush seat of the Reach surrounded by golden rose gardens and fertile fields." },
        { id: 'eyrie', name: 'The Eyrie', house: 'House Arryn', coords: { x: 60, y: 38 }, desc: "Impregnable mountain stronghold perched high above the Vale of Arryn." }
      ],

      battles: [
        {
          id: 'field_of_fire',
          name: 'The Field of Fire (2 BC)',
          location: 'The Reach Plains',
          commanders: 'Aegon I & Sisters vs. Mern IX & Loren Lannister',
          outcome: 'Decisive Targaryen Victory',
          details: 'The only battle in history where all three dragons—Balerion, Vhagar, and Meraxes—were deployed simultaneously. 4,000 allied soldiers burned in minutes.'
        },
        {
          id: 'blackwater',
          name: 'Battle of the Blackwater (299 AC)',
          location: 'King\'s Landing Bay',
          commanders: 'Tyrion Lannister / Tywin vs. Stannis Baratheon',
          outcome: 'Lannister-Tyrell Coalition Victory',
          details: 'Stannis\' fleet was decimated by Tyrion\'s Wildfire trap before Tywin and the Tyrell vanguard smashed Stannis\' flank.'
        },
        {
          id: 'bastards',
          name: 'Battle of the Bastards (303 AC)',
          location: 'Outside Winterfell',
          commanders: 'Jon Snow vs. Ramsay Bolton',
          outcome: 'Stark Victory (Arryn Cavalry Intervention)',
          details: 'Fierce melee battle featuring a shield wall body pile, broken by the surprise charge of the Knights of the Vale.'
        }
      ]
    };

    // --- MAIN APP CONTAINER ---
    function App() {
      const [view, setView] = useState('home'); // home, timeline, houses, dragons, map, warroom
      const [selectedEra, setSelectedEra] = useState('all');
      const [selectedHouse, setSelectedHouse] = useState(LORE_DATABASE.houses[0]);
      const [selectedDragon, setSelectedDragon] = useState(LORE_DATABASE.dragons[0]);
      const [audioMuted, setAudioMuted] = useState(true);
      const [searchQuery, setSearchQuery] = useState('');

      useEffect(() => {
        if (window.lucide) {
          window.lucide.createIcons();
        }
      }, [view, selectedHouse, selectedDragon]);

      return (
        <div className="min-h-screen flex flex-col bg-obsidian text-parchment relative overflow-hidden">
          {/* TOP NAVIGATION BAR */}
          <header className="sticky top-0 z-50 bg-charcoal/90 backdrop-blur border-b border-gold/30 px-6 py-4 flex flex-wrap justify-between items-center shadow-2xl">
            <div 
              className="flex items-center space-x-3 cursor-pointer group"
              onClick={() => setView('home')}
            >
              <div className="w-10 h-10 rounded-full border border-gold flex items-center justify-center bg-targaryen/40 group-hover:scale-110 transition">
                <span className="text-gold font-bold text-xl">W</span>
              </div>
              <div>
                <h1 className="font-medieval text-xl font-bold tracking-widest text-gold-bright group-hover:text-gold transition">
                  WESTEROS
                </h1>
                <p className="text-[10px] text-parchment-dark tracking-widest uppercase">Archive & Strategy Room</p>
              </div>
            </div>

            {/* NAV LINKS */}
            <nav className="flex space-x-1 sm:space-x-4 my-2 sm:my-0 text-xs sm:text-sm font-semibold uppercase tracking-wider">
              {[
                { id: 'timeline', label: 'Chronicles', icon: 'book-open' },
                { id: 'houses', label: 'Great Houses', icon: 'shield' },
                { id: 'dragons', label: 'Dragons', icon: 'flame' },
                { id: 'map', label: 'Interactive Map', icon: 'map-pin' },
                { id: 'warroom', label: 'War Simulator', icon: 'swords', highlight: true }
              ].map(item => (
                <button
                  key={item.id}
                  onClick={() => setView(item.id)}
                  className={`px-3 py-2 rounded border transition flex items-center space-x-1.5 ${
                    view === item.id 
                      ? 'bg-gold/20 border-gold text-gold-bright shadow-[0_0_10px_rgba(212,175,55,0.3)]' 
                      : item.highlight 
                        ? 'border-targaryen bg-targaryen/30 text-red-300 hover:bg-targaryen/60'
                        : 'border-transparent text-parchment/70 hover:text-gold hover:border-gold/40'
                  }`}
                >
                  <i data-lucide={item.icon} className="w-4 h-4"></i>
                  <span>{item.label}</span>
                </button>
              ))}
            </nav>

            {/* QUICK SEARCH & AUDIO CONTROL */}
            <div className="flex items-center space-x-3">
              <div className="relative hidden md:block">
                <input
                  type="text"
                  placeholder="Search Westeros lore..."
                  value={searchQuery}
                  onChange={(e) => setSearchQuery(e.target.value)}
                  className="bg-obsidian/80 border border-gold/30 rounded-full text-xs px-3 py-1.5 pl-8 text-parchment focus:outline-none focus:border-gold w-44 focus:w-60 transition-all"
                />
                <i data-lucide="search" className="w-3.5 h-3.5 absolute left-2.5 top-2.5 text-parchment-dark"></i>
              </div>

              <button 
                onClick={() => setAudioMuted(!audioMuted)}
                className="p-2 border border-gold/30 rounded-full hover:border-gold text-gold hover:bg-gold/10 transition"
                title={audioMuted ? "Unmute atmospheric ambiance" : "Mute ambiance"}
              >
                <i data-lucide={audioMuted ? "volume-x" : "volume-2"} className="w-4 h-4"></i>
              </button>
            </div>
          </header>

          {/* MAIN CONTENT AREA ROUTER */}
          <main className="flex-grow relative z-10">
            {view === 'home' && <HomeView setView={setView} />}
            {view === 'timeline' && <TimelineView selectedEra={selectedEra} setSelectedEra={setSelectedEra} />}
            {view === 'houses' && <HousesView selectedHouse={selectedHouse} setSelectedHouse={setSelectedHouse} />}
            {view === 'dragons' && <DragonsView selectedDragon={selectedDragon} setSelectedDragon={setSelectedDragon} />}
            {view === 'map' && <MapView />}
            {view === 'warroom' && <WarSimulatorView />}
          </main>

          {/* FOOTER */}
          <footer className="border-t border-gold/20 bg-charcoal/80 py-6 px-8 text-center text-xs text-parchment-dark flex flex-col md:flex-row justify-between items-center gap-4">
            <p>© THE WORLD OF WESTEROS — High Fantasy Interactive Lore Archive & Strategy Engine.</p>
            <div className="flex space-x-6 text-gold/70">
              <a href="#" onClick={(e) => { e.preventDefault(); setView('timeline'); }} className="hover:text-gold">Targaryen Lineage</a>
              <a href="#" onClick={(e) => { e.preventDefault(); setView('houses'); }} className="hover:text-gold">House Arms</a>
              <a href="#" onClick={(e) => { e.preventDefault(); setView('warroom'); }} className="hover:text-gold">Tactical Simulator</a>
            </div>
          </footer>
        </div>
      );
    }

    // =========================================================================
    // 1. HOMEPAGE VIEW WITH CINEMATIC HERO & 3D CANVAS EMBERS
    // =========================================================================
    function HomeView({ setView }) {
      const canvasRef = useRef(null);

      // Three.js Background Ember Particles
      useEffect(() => {
        if (!canvasRef.current) return;
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ canvas: canvasRef.current, alpha: true, antialias: true });
        
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

        // Create Embers
        const particleCount = 200;
        const geometry = new THREE.BufferGeometry();
        const positions = new Float32Array(particleCount * 3);
        const scales = new Float32Array(particleCount);

        for (let i = 0; i < particleCount * 3; i += 3) {
          positions[i] = (Math.random() - 0.5) * 20;
          positions[i + 1] = (Math.random() - 0.5) * 20;
          positions[i + 2] = (Math.random() - 0.5) * 20;
        }

        geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

        const material = new THREE.PointsMaterial({
          color: 0xd4af37,
          size: 0.08,
          transparent: true,
          opacity: 0.7,
          blending: THREE.AdditiveBlending
        });

        const particles = new THREE.Points(geometry, material);
        scene.add(particles);

        camera.position.z = 8;

        let reqId;
        const animate = () => {
          reqId = requestAnimationFrame(animate);
          const pos = geometry.attributes.position.array;
          for (let i = 1; i < particleCount * 3; i += 3) {
            pos[i] += 0.015;
            if (pos[i] > 10) pos[i] = -10;
          }
          geometry.attributes.position.needsUpdate = true;
          particles.rotation.y += 0.001;
          renderer.render(scene, camera);
        };
        animate();

        const handleResize = () => {
          camera.aspect = window.innerWidth / window.innerHeight;
          camera.updateProjectionMatrix();
          renderer.setSize(window.innerWidth, window.innerHeight);
        };
        window.addEventListener('resize', handleResize);

        return () => {
          cancelAnimationFrame(reqId);
          window.removeEventListener('resize', handleResize);
        };
      }, []);

      return (
        <div className="relative min-h-[90vh] flex flex-col items-center justify-center text-center px-4 overflow-hidden bg-embers">
          {/* 3D Ember Background Canvas */}
          <canvas ref={canvasRef} className="absolute inset-0 pointer-events-none z-0" />

          {/* Vignette & Ambient Gradient overlay */}
          <div className="absolute inset-0 bg-radial-vignette pointer-events-none bg-gradient-to-t from-obsidian via-transparent to-obsidian opacity-90 z-0"></div>

          {/* Hero Content */}
          <div className="relative z-10 max-w-4xl mx-auto space-y-6 py-12">
            <div className="inline-block border-y border-gold/40 py-1 px-6 mb-2">
              <span className="text-xs uppercase tracking-[0.3em] text-gold font-semibold">
                An Immersive Fantasy Encyclopedia & War Room
              </span>
            </div>

            <h1 className="font-medieval text-4xl sm:text-6xl md:text-7xl font-black text-transparent bg-clip-text bg-gradient-to-r from-parchment via-gold-bright to-parchment drop-shadow-[0_10px_20px_rgba(0,0,0,0.9)] tracking-wider leading-tight">
              THE WORLD OF WESTEROS
            </h1>

            <p className="text-lg sm:text-xl text-parchment/80 font-light max-w-2xl mx-auto italic">
              “From the Conquest of Aegon to the Fall of the Dragon Queen.”
            </p>

            <div className="p-6 bg-charcoal/70 border border-gold/30 rounded-lg backdrop-blur-md shadow-2xl max-w-2xl mx-auto text-sm text-parchment/90 leading-relaxed text-justify border-gold-engraved">
              Step into the Great Hall of the Red Keep. Explore three centuries of Targaryen reign, the tragic Dance of the Dragons, genealogies of the noble bloodlines, and direct battlefield command in real-time tactical simulations.
            </div>

            {/* ACTION BUTTONS */}
            <div className="flex flex-col sm:flex-row justify-center items-center gap-4 pt-4">
              <button
                onClick={() => setView('timeline')}
                className="w-full sm:w-auto px-8 py-4 bg-gradient-to-r from-gold/80 to-amber-600 hover:from-gold hover:to-amber-500 text-obsidian font-bold rounded shadow-lg hover:shadow-[0_0_25px_rgba(212,175,55,0.6)] transition-all transform hover:-translate-y-0.5 flex items-center justify-center space-x-2"
              >
                <i data-lucide="compass" className="w-5 h-5"></i>
                <span>ENTER ARCHIVES & CHRONICLES</span>
              </button>

              <button
                onClick={() => setView('warroom')}
                className="w-full sm:w-auto px-8 py-4 bg-targaryen/80 hover:bg-targaryen text-red-100 border border-red-500/50 font-bold rounded shadow-lg hover:shadow-[0_0_25px_rgba(139,0,0,0.7)] transition-all transform hover:-translate-y-0.5 flex items-center justify-center space-x-2"
              >
                <i data-lucide="swords" className="w-5 h-5"></i>
                <span>COMMAND WAR ROOM</span>
              </button>
            </div>

            {/* ERA QUICK SELECTOR GRID */}
            <div className="pt-10">
              <p className="text-xs font-semibold text-gold/60 uppercase tracking-widest mb-3">Key Historical Epochs</p>
              <div className="grid grid-cols-2 sm:grid-cols-4 gap-2 text-xs">
                {LORE_DATABASE.eras.map(era => (
                  <button
                    key={era.id}
                    onClick={() => setView('timeline')}
                    className="p-2.5 bg-steel/40 border border-gold/20 hover:border-gold/70 rounded text-left transition hover:bg-gold/10 group"
                  >
                    <div className="font-bold text-gold group-hover:text-gold-bright truncate">{era.name}</div>
                    <div className="text-[10px] text-parchment-dark">{era.period}</div>
                  </button>
                ))}
              </div>
            </div>
          </div>
        </div>
      );
    }

    // =========================================================================
    // 2. TIMELINE & CHRONICLES VIEW
    // =========================================================================
    function TimelineView({ selectedEra, setSelectedEra }) {
      return (
        <div className="max-w-6xl mx-auto px-6 py-10 space-y-8">
          <div className="border-b border-gold/30 pb-4">
            <h2 className="font-medieval text-3xl font-bold text-gold-bright">CHRONICLES OF WESTEROS</h2>
            <p className="text-sm text-parchment-dark">Detailed historical record from the Dawn Age through the Dragon Queen's downfall.</p>
          </div>

          {/* ERA FILTER BUTTONS */}
          <div className="flex flex-wrap gap-2">
            <button
              onClick={() => setSelectedEra('all')}
              className={`px-3 py-1.5 rounded text-xs uppercase font-bold transition ${
                selectedEra === 'all' ? 'bg-gold text-obsidian' : 'bg-steel/60 text-parchment hover:bg-steel'
              }`}
            >
              All Eras
            </button>
            {LORE_DATABASE.eras.map(era => (
              <button
                key={era.id}
                onClick={() => setSelectedEra(era.id)}
                className={`px-3 py-1.5 rounded text-xs uppercase font-bold transition ${
                  selectedEra === era.id ? 'bg-gold text-obsidian' : 'bg-steel/60 text-parchment hover:bg-steel'
                }`}
              >
                {era.name}
              </button>
            ))}
          </div>

          {/* CHRONOLOGICAL CARDS */}
          <div className="relative border-l-2 border-gold/30 ml-4 pl-6 space-y-8">
            {/* Event 1: Aegon's Conquest */}
            <div className="relative group">
              <div className="absolute -left-[31px] top-1.5 w-4 h-4 rounded-full bg-gold border-2 border-obsidian shadow-[0_0_10px_#d4af37]"></div>
              <div className="bg-charcoal/80 border border-gold/30 p-6 rounded-lg shadow-xl hover:border-gold/70 transition space-y-3">
                <div className="flex justify-between items-start flex-wrap gap-2">
                  <span className="text-xs font-bold px-2 py-0.5 bg-targaryen/40 border border-targaryen text-red-200 rounded">
                    2 BC - 1 AC
                  </span>
                  <span className="text-xs text-gold/70 font-mono">Location: Highfield / Harrenhal / King's Landing</span>
                </div>
                <h3 className="text-xl font-bold font-medieval text-gold-bright">Aegon's Conquest & Creation of the Iron Throne</h3>
                <p className="text-sm leading-relaxed text-parchment/90">
                  Aegon Targaryen and his sister-wives Visenya and Rhaenys landed at the mouth of the Blackwater Rush with three massive dragonlords dragons: Balerion, Vhagar, and Meraxes. Over two years, they forced six of the Seven Kingdoms to submit, burning Harrenhal and annihilating combined hosts at the Field of Fire.
                </p>
                <div className="grid sm:grid-cols-2 gap-4 text-xs pt-2 border-t border-gold/20">
                  <div><strong className="text-gold">Key Figures:</strong> Aegon I, Visenya, Rhaenys, Torrhen Stark</div>
                  <div><strong className="text-gold">Dragons Involved:</strong> Balerion, Vhagar, Meraxes</div>
                </div>
              </div>
            </div>

            {/* Event 2: Dance of the Dragons */}
            <div className="relative group">
              <div className="absolute -left-[31px] top-1.5 w-4 h-4 rounded-full bg-targaryen border-2 border-obsidian shadow-[0_0_10px_#8b0000]"></div>
              <div className="bg-charcoal/80 border border-gold/30 p-6 rounded-lg shadow-xl hover:border-gold/70 transition space-y-3">
                <div className="flex justify-between items-start flex-wrap gap-2">
                  <span className="text-xs font-bold px-2 py-0.5 bg-red-900/50 border border-red-600 text-red-300 rounded">
                    129 AC - 131 AC
                  </span>
                  <span className="text-xs text-gold/70 font-mono">Location: King's Landing / Dragonstone / Gods Eye</span>
                </div>
                <h3 className="text-xl font-bold font-medieval text-gold-bright">The Dance of the Dragons Civil War</h3>
                <p className="text-sm leading-relaxed text-parchment/90">
                  Following the death of Viserys I, a violent civil war erupted between the "Blacks" (supporting King's chosen heir Rhaenyra) and the "Greens" (supporting Aegon II). Dragon-on-dragon aerial duels decimated both sides and crippled Targaryen dragon power permanently.
                </p>
                <div className="grid sm:grid-cols-2 gap-4 text-xs pt-2 border-t border-gold/20">
                  <div><strong className="text-gold">Key Figures:</strong> Rhaenyra, Aegon II, Daemon, Aemond One-Eye</div>
                  <div><strong className="text-gold">Casualties:</strong> Most Targaryen Dragons & Royal Family members</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      );
    }

    // =========================================================================
    // 3. GREAT HOUSES DISCOVERY VIEW
    // =========================================================================
    function HousesView({ selectedHouse, setSelectedHouse }) {
      return (
        <div className="max-w-6xl mx-auto px-6 py-10 space-y-8">
          <div className="border-b border-gold/30 pb-4">
            <h2 className="font-medieval text-3xl font-bold text-gold-bright">GREAT HOUSES & NOBLE LINEAGES</h2>
            <p className="text-sm text-parchment-dark">The high lords who rule the nine realms of Westeros.</p>
          </div>

          <div className="grid md:grid-cols-3 gap-8">
            {/* HOUSE SELECTION LIST */}
            <div className="space-y-3">
              {LORE_DATABASE.houses.map(house => (
                <div
                  key={house.id}
                  onClick={() => setSelectedHouse(house)}
                  className={`p-4 rounded-lg border cursor-pointer transition flex items-center justify-between ${
                    selectedHouse.id === house.id
                      ? 'border-gold bg-gold/10 shadow-lg'
                      : 'border-gold/20 bg-charcoal/60 hover:border-gold/40'
                  }`}
                >
                  <div>
                    <h4 className="font-medieval font-bold text-lg" style={{ color: house.accent }}>{house.name}</h4>
                    <p className="text-xs italic text-parchment-dark">"{house.words}"</p>
                  </div>
                  <i data-lucide="chevron-right" className="w-5 h-5 text-gold"></i>
                </div>
              ))}
            </div>

            {/* DETAILED PROFILE CARD */}
            <div className="md:col-span-2 bg-charcoal/90 border border-gold/40 p-6 rounded-lg shadow-2xl space-y-6 border-gold-engraved">
              <div className="border-b border-gold/20 pb-4 flex justify-between items-start">
                <div>
                  <h3 className="font-medieval text-3xl font-black" style={{ color: selectedHouse.accent }}>
                    {selectedHouse.name}
                  </h3>
                  <p className="text-sm font-semibold italic text-gold">"{selectedHouse.words}"</p>
                </div>
                <span className="text-xs px-3 py-1 bg-obsidian border border-gold/40 rounded text-parchment-dark">
                  Seat: {selectedHouse.seat}
                </span>
              </div>

              <div className="grid sm:grid-cols-2 gap-4 text-xs">
                <div className="p-3 bg-obsidian/60 border border-gold/20 rounded">
                  <span className="text-gold font-bold block mb-1">Region & Territory:</span>
                  {selectedHouse.region}
                </div>
                <div className="p-3 bg-obsidian/60 border border-gold/20 rounded">
                  <span className="text-gold font-bold block mb-1">Military & Arms Strength:</span>
                  {selectedHouse.military}
                </div>
              </div>

              <div>
                <h4 className="font-medieval text-md text-gold font-bold mb-2">Origins & House History</h4>
                <p className="text-xs sm:text-sm text-parchment/80 leading-relaxed text-justify">
                  {selectedHouse.history}
                </p>
              </div>

              {/* FAMILY TREE & NOTABLE MONARCHS */}
              <div>
                <h4 className="font-medieval text-md text-gold font-bold mb-3">Key Lineage & Ancestry</h4>
                <div className="space-y-2">
                  {selectedHouse.tree.map((member, idx) => (
                    <div key={idx} className="p-2.5 bg-steel/30 border border-gold/10 rounded text-xs flex justify-between items-center">
                      <div>
                        <strong className="text-gold-bright">{member.name}</strong>
                        <span className="text-parchment-dark ml-2">({member.spouse})</span>
                      </div>
                      <span className="text-[10px] text-gold/70 italic">{member.claim}</span>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          </div>
        </div>
      );
    }

    // =========================================================================
    // 4. DRAGON ENCYCLOPEDIA VIEW
    // =========================================================================
    function DragonsView({ selectedDragon, setSelectedDragon }) {
      return (
        <div className="max-w-6xl mx-auto px-6 py-10 space-y-8">
          <div className="border-b border-gold/30 pb-4">
            <h2 className="font-medieval text-3xl font-bold text-gold-bright">DRAGONS OF THE TARGARYEN DYNASTY</h2>
            <p className="text-sm text-parchment-dark">The apex flying beasts of Old Valyria that conquered Westeros.</p>
          </div>

          <div className="grid md:grid-cols-3 gap-8">
            <div className="space-y-3">
              {LORE_DATABASE.dragons.map(dragon => (
                <div
                  key={dragon.id}
                  onClick={() => setSelectedDragon(dragon)}
                  className={`p-4 rounded-lg border cursor-pointer transition flex items-center justify-between ${
                    selectedDragon.id === dragon.id
                      ? 'border-red-600 bg-targaryen/20 shadow-lg'
                      : 'border-gold/20 bg-charcoal/60 hover:border-gold/40'
                  }`}
                >
                  <div>
                    <h4 className="font-medieval font-bold text-lg text-red-400">{dragon.name}</h4>
                    <p className="text-xs text-parchment-dark">Rider: {dragon.rider}</p>
                  </div>
                  <i data-lucide="flame" className="w-5 h-5 text-red-500"></i>
                </div>
              ))}
            </div>

            <div className="md:col-span-2 bg-charcoal/90 border border-red-900/50 p-6 rounded-lg shadow-2xl space-y-6">
              <div className="border-b border-red-900/30 pb-4 flex justify-between items-start">
                <div>
                  <h3 className="font-medieval text-3xl font-black text-red-500">{selectedDragon.name}</h3>
                  <p className="text-sm text-gold">Primary Rider(s): {selectedDragon.rider}</p>
                </div>
                <span className="text-xs px-3 py-1 bg-red-950 border border-red-800 rounded text-red-300">
                  {selectedDragon.status}
                </span>
              </div>

              <div className="grid sm:grid-cols-2 gap-4 text-xs">
                <div className="p-3 bg-obsidian border border-gold/20 rounded">
                  <span className="text-gold font-bold block mb-1">Estimated Size & Length:</span>
                  {selectedDragon.lengthMetres} meters (Wingspan ~{selectedDragon.wingspanMetres}m)
                </div>
                <div className="p-3 bg-obsidian border border-gold/20 rounded">
                  <span className="text-gold font-bold block mb-1">Scale & Fire Aesthetics:</span>
                  Scales: {selectedDragon.scaleColor} | Flame: {selectedDragon.flameColor}
                </div>
              </div>

              <div>
                <h4 className="font-medieval text-md text-gold font-bold mb-2">Combat Legend & Historical Impact</h4>
                <p className="text-xs sm:text-sm text-parchment/80 leading-relaxed text-justify">
                  {selectedDragon.history}
                </p>
              </div>
            </div>
          </div>
        </div>
      );
    }

    // =========================================================================
    // 5. INTERACTIVE MAP OF WESTEROS
    // =========================================================================
    function MapView() {
      const [activeLoc, setActiveLoc] = useState(LORE_DATABASE.mapLocations[0]);

      return (
        <div className="max-w-6xl mx-auto px-6 py-10 space-y-6">
          <div className="border-b border-gold/30 pb-4">
            <h2 className="font-medieval text-3xl font-bold text-gold-bright">CARTOGRAPHY OF WESTEROS</h2>
            <p className="text-sm text-parchment-dark">Select key strategic seats and strongholds across the Seven Kingdoms.</p>
          </div>

          <div className="grid lg:grid-cols-3 gap-6">
            {/* CANVAS INTERACTIVE MAP CONTAINER */}
            <div className="lg:col-span-2 bg-charcoal/90 border border-gold/30 rounded-lg h-[500px] relative overflow-hidden flex items-center justify-center p-4">
              {/* Stylized Westeros Map Graphic Backdrop */}
              <div className="absolute inset-0 bg-[radial-gradient(#1e2330_1px,transparent_1px)] [background-size:16px_16px] opacity-40"></div>
              
              <div className="relative w-full h-full border border-gold/20 bg-obsidian/80 rounded flex items-center justify-center">
                <p className="absolute top-4 left-4 text-xs font-mono text-gold/60 uppercase">The Seven Kingdoms & Beyond</p>

                {/* MAP LOCATION PINS */}
                {LORE_DATABASE.mapLocations.map(loc => (
                  <button
                    key={loc.id}
                    onClick={() => setActiveLoc(loc)}
                    style={{ left: `${loc.coords.x}%`, top: `${loc.coords.y}%` }}
                    className={`absolute transform -translate-x-1/2 -translate-y-1/2 p-2 rounded-full transition group ${
                      activeLoc.id === loc.id 
                        ? 'bg-gold text-obsidian scale-125 z-20 shadow-[0_0_15px_#d4af37]' 
                        : 'bg-steel text-gold border border-gold/40 hover:scale-110 z-10'
                    }`}
                  >
                    <i data-lucide="map-pin" className="w-4 h-4"></i>
                    <span className="absolute left-6 top-0 bg-obsidian/90 border border-gold/40 px-2 py-0.5 rounded text-[10px] text-gold font-bold whitespace-nowrap opacity-0 group-hover:opacity-100 transition">
                      {loc.name}
                    </span>
                  </button>
                ))}
              </div>
            </div>

            {/* LOCATION DETAILS PANEL */}
            <div className="bg-charcoal/90 border border-gold/40 p-6 rounded-lg space-y-4 shadow-xl border-gold-engraved">
              <h3 className="font-medieval text-2xl font-bold text-gold-bright">{activeLoc.name}</h3>
              <div className="text-xs text-gold font-semibold">Ruling Dynasty: {activeLoc.house}</div>
              <p className="text-xs sm:text-sm text-parchment/80 leading-relaxed">
                {activeLoc.desc}
              </p>
            </div>
          </div>
        </div>
      );
    }

    // =========================================================================
    // 6. WAR ROOM — REAL-TIME PLAYABLE MEDIEVAL WAR SIMULATOR
    // =========================================================================
    function WarSimulatorView() {
      const canvasRef = useRef(null);
      const [battleState, setBattleState] = useState('menu'); // menu, playing, victory, defeat
      const [playerArmy, setPlayerArmy] = useState('stark');
      const [enemyArmy, setEnemyArmy] = useState('lannister');
      const [dragonDeployable, setDragonDeployable] = useState(true);
      const [stats, setStats] = useState({ playerRemaining: 100, enemyRemaining: 100, dragonHealth: 100 });

      // SIMULATION ENGINE (CANVAS 2D PHYSICS & BATTLE AI)
      useEffect(() => {
        if (battleState !== 'playing' || !canvasRef.current) return;

        const canvas = canvasRef.current;
        const ctx = canvas.getContext('2d');
        let animId;

        // Unit Entities
        let units = [];
        const UNIT_COUNT = 80;

        // Player Units (Left side)
        for (let i = 0; i < UNIT_COUNT; i++) {
          units.push({
            x: 50 + Math.random() * 120,
            y: 50 + Math.random() * 300,
            team: 'player',
            hp: 100,
            speed: 1 + Math.random() * 0.5,
            color: playerArmy === 'stark' ? '#4a6572' : '#8b0000'
          });
        }

        // Enemy Units (Right side)
        for (let i = 0; i < UNIT_COUNT; i++) {
          units.push({
            x: canvas.width - (50 + Math.random() * 120),
            y: 50 + Math.random() * 300,
            team: 'enemy',
            hp: 100,
            speed: 0.9 + Math.random() * 0.5,
            color: enemyArmy === 'lannister' ? '#800020' : '#d4af37'
          });
        }

        // Dragon Entity
        let dragon = {
          x: 100,
          y: 200,
          targetX: 300,
          targetY: 200,
          active: dragonDeployable,
          breathCooldown: 0
        };

        const updateSimulation = () => {
          ctx.clearRect(0, 0, canvas.width, canvas.height);

          // Draw Battlefield Terrain (Mud & Snow)
          ctx.fillStyle = '#12151c';
          ctx.fillRect(0, 0, canvas.width, canvas.height);

          // Render Units & AI Melee Movement
          let playerAlive = 0;
          let enemyAlive = 0;

          units.forEach((u, idx) => {
            if (u.hp <= 0) return;

            if (u.team === 'player') playerAlive++;
            else enemyAlive++;

            // Find nearest opponent
            let nearest = null;
            let minDist = 9999;

            units.forEach((other, oIdx) => {
              if (idx === oIdx || other.team === u.team || other.hp <= 0) return;
              const dist = Math.hypot(other.x - u.x, other.y - u.y);
              if (dist < minDist) {
                minDist = dist;
                nearest = other;
              }
            });

            // Move towards opponent
            if (nearest) {
              const angle = Math.atan2(nearest.y - u.y, nearest.x - u.x);
              u.x += Math.cos(angle) * u.speed;
              u.y += Math.sin(angle) * u.speed;

              // Attack condition
              if (minDist < 6) {
                nearest.hp -= 1.5;
              }
            }

            // Draw Soldier
            ctx.fillStyle = u.color;
            ctx.beginPath();
            ctx.arc(u.x, u.y, 3, 0, Math.PI * 2);
            ctx.fill();
          });

          // Dragon Control & Breath Fire FX
          if (dragon.active) {
            // Fly toward enemy cluster
            const dx = dragon.targetX - dragon.x;
            const dy = dragon.targetY - dragon.y;
            dragon.x += dx * 0.03;
            dragon.y += dy * 0.03;

            // Draw Dragon Shadow
            ctx.fillStyle = 'rgba(0,0,0,0.4)';
            ctx.beginPath();
            ctx.ellipse(dragon.x, dragon.y + 20, 20, 10, 0, 0, Math.PI * 2);
            ctx.fill();

            // Draw Dragon Sprite/Wings
            ctx.fillStyle = '#ff2200';
            ctx.beginPath();
            ctx.arc(dragon.x, dragon.y, 8, 0, Math.PI * 2);
            ctx.fill();

            // Dragon Fire Blast on enemy cluster
            ctx.fillStyle = 'rgba(255, 100, 0, 0.4)';
            ctx.beginPath();
            ctx.arc(dragon.x, dragon.y, 35, 0, Math.PI * 2);
            ctx.fill();

            // Damage enemy units in dragonfire radius
            units.forEach(u => {
              if (u.team === 'enemy' && u.hp > 0) {
                const d = Math.hypot(u.x - dragon.x, u.y - dragon.y);
                if (d < 35) {
                  u.hp -= 2.5; // Scorching damage
                }
              }
            });
          }

          // Update Stats State
          setStats({
            playerRemaining: Math.round((playerAlive / UNIT_COUNT) * 100),
            enemyRemaining: Math.round((enemyAlive / UNIT_COUNT) * 100),
            dragonHealth: 100
          });

          // Check Win/Loss conditions
          if (enemyAlive <= 0) setBattleState('victory');
          else if (playerAlive <= 0) setBattleState('defeat');
          else animId = requestAnimationFrame(updateSimulation);
        };

        animId = requestAnimationFrame(updateSimulation);

        // Canvas Click -> Direct Dragon Fire Target
        const handleCanvasClick = (e) => {
          const rect = canvas.getBoundingClientRect();
          dragon.targetX = e.clientX - rect.left;
          dragon.targetY = e.clientY - rect.top;
        };

        canvas.addEventListener('click', handleCanvasClick);

        return () => {
          cancelAnimationFrame(animId);
          canvas.removeEventListener('click', handleCanvasClick);
        };
      }, [battleState]);

      return (
        <div className="max-w-6xl mx-auto px-6 py-10 space-y-6">
          <div className="border-b border-gold/30 pb-4 flex justify-between items-center flex-wrap gap-4">
            <div>
              <h2 className="font-medieval text-3xl font-bold text-gold-bright">THE WAR ROOM SIMULATOR</h2>
              <p className="text-sm text-parchment-dark">Command battalions and maneuver dragonfire across real-time battlefields.</p>
            </div>

            {battleState === 'playing' && (
              <button
                onClick={() => setBattleState('menu')}
                className="px-4 py-2 bg-steel border border-gold/30 text-xs font-bold rounded hover:bg-gold/20 text-gold"
              >
                Retreat to War Council
              </button>
            )}
          </div>

          {battleState === 'menu' ? (
            /* PRE-BATTLE TACTICAL SETUP MENU */
            <div className="bg-charcoal/90 border border-gold/40 p-8 rounded-lg shadow-2xl space-y-6 border-gold-engraved max-w-2xl mx-auto">
              <h3 className="font-medieval text-2xl font-bold text-gold text-center">Battle Setup & Armaments</h3>

              <div className="grid grid-cols-2 gap-4">
                <div>
                  <label className="text-xs text-gold font-bold block mb-2">Vanguard Host (Player):</label>
                  <select 
                    value={playerArmy}
                    onChange={(e) => setPlayerArmy(e.target.value)}
                    className="w-full bg-obsidian border border-gold/30 rounded p-2 text-xs text-parchment"
                  >
                    <option value="stark">House Stark (Winterfell Host)</option>
                    <option value="targaryen">House Targaryen (Dragonlords)</option>
                  </select>
                </div>

                <div>
                  <label className="text-xs text-gold font-bold block mb-2">Enemy Host (Opponent AI):</label>
                  <select 
                    value={enemyArmy}
                    onChange={(e) => setEnemyArmy(e.target.value)}
                    className="w-full bg-obsidian border border-gold/30 rounded p-2 text-xs text-parchment"
                  >
                    <option value="lannister">House Lannister (Crown Host)</option>
                    <option value="baratheon">House Baratheon (Storm Host)</option>
                  </select>
                </div>
              </div>

              <div className="flex items-center space-x-3 p-3 bg-obsidian border border-gold/20 rounded">
                <input
                  type="checkbox"
                  id="dragonCheck"
                  checked={dragonDeployable}
                  onChange={(e) => setDragonDeployable(e.target.checked)}
                  className="rounded accent-gold"
                />
                <label htmlFor="dragonCheck" className="text-xs text-parchment cursor-pointer">
                  Deploy Royal Dragon Air Support (Click canvas to direct flame strikes)
                </label>
              </div>

              <button
                onClick={() => setBattleState('playing')}
                className="w-full py-4 bg-gradient-to-r from-gold/80 to-amber-600 hover:from-gold hover:to-amber-500 text-obsidian font-bold rounded shadow-xl tracking-wider text-sm transition uppercase"
              >
                COMMENCE BATTLE SIMULATION
              </button>
            </div>
          ) : (
            /* REAL-TIME BATTLE CANVAS ENGINE */
            <div className="space-y-4">
              {/* BATTLE TACTICAL HUD */}
              <div className="grid grid-cols-3 gap-4 text-center">
                <div className="p-3 bg-charcoal border border-gold/30 rounded">
                  <span className="text-[10px] text-parchment-dark block">PLAYER HOST STRENGTH</span>
                  <span className="text-xl font-bold text-gold">{stats.playerRemaining}%</span>
                </div>
                <div className="p-3 bg-charcoal border border-red-900/50 rounded">
                  <span className="text-[10px] text-parchment-dark block">DRAGON AIR COMBAT</span>
                  <span className="text-xl font-bold text-red-500">{dragonDeployable ? 'ACTIVE' : 'NONE'}</span>
                </div>
                <div className="p-3 bg-charcoal border border-gold/30 rounded">
                  <span className="text-[10px] text-parchment-dark block">ENEMY HOST STRENGTH</span>
                  <span className="text-xl font-bold text-red-400">{stats.enemyRemaining}%</span>
                </div>
              </div>

              {/* INTERACTIVE SIMULATION CANVAS */}
              <div className="relative border-2 border-gold/40 rounded-lg overflow-hidden shadow-2xl bg-obsidian flex justify-center">
                <canvas
                  ref={canvasRef}
                  width={750}
                  height={380}
                  className="w-full max-w-[750px] cursor-crosshair"
                />

                {/* OVERLAY VICTORY / DEFEAT SCREEN */}
                {battleState !== 'playing' && (
                  <div className="absolute inset-0 bg-obsidian/90 backdrop-blur flex flex-col items-center justify-center space-y-4 z-30">
                    <h3 className={`font-medieval text-4xl font-bold ${battleState === 'victory' ? 'text-gold' : 'text-red-600'}`}>
                      {battleState === 'victory' ? 'VICTORY IS YOURS' : 'YOUR HOST IS SLAIN'}
                    </h3>
                    <button
                      onClick={() => setBattleState('menu')}
                      className="px-6 py-3 bg-gold text-obsidian font-bold rounded hover:bg-gold-bright transition"
                    >
                      RETURN TO WAR ROOM
                    </button>
                  </div>
                )}
              </div>
              <p className="text-[11px] text-center text-parchment-dark italic">
                * Click anywhere on the battlefield canvas to order your dragon to unleash dragonfire at that point.
              </p>
            </div>
          )}
        </div>
      );
    }

    // Render React Root App
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
