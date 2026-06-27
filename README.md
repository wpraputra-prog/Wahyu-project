<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wahyu Audio Project - Pro DSP v8</title>
    <style>
        :root {
            --bg-main: #08080a;
            --bg-panel: #101013;
            --bg-darker: #040404;
            --accent-blue: #00e5ff;
            --accent-red: #ff2a55;
            --accent-hover: #3a7bd5;
            --text-main: #e2e8f0;
            --text-muted: #8b949e;
            --border: #2d3139;
            --border-light: #3e4451;
            --radius: 6px;
            --led-green: #00ff41;
            --led-yellow: #ffea00;
            --led-red: #ff003c;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: 'Inter', 'Segoe UI', system-ui, sans-serif;
            background-color: var(--bg-main);
            color: var(--text-main);
            padding: 20px;
            min-height: 100vh;
            background-image: 
                linear-gradient(rgba(255, 255, 255, 0.02) 1px, transparent 1px),
                linear-gradient(90deg, rgba(255, 255, 255, 0.02) 1px, transparent 1px);
            background-size: 30px 30px;
        }

        .container { width: 100%; max-width: 1550px; margin: 0 auto; display: flex; flex-direction: column; gap: 20px; }
        
        .panel { 
            background: linear-gradient(180deg, var(--bg-panel) 0%, var(--bg-darker) 100%); 
            border: 1px solid var(--border); 
            border-radius: var(--radius); 
            padding: 25px; 
            box-shadow: 0 15px 50px rgba(0, 0, 0, 0.9), inset 0 1px 0 rgba(255,255,255,0.03); 
        }
        
        .header { 
            display: flex; justify-content: space-between; align-items: center; 
            padding-bottom: 20px; border-bottom: 1px solid var(--border); margin-bottom: 25px; 
        }
        .header h1 { 
            font-size: 2.2rem; letter-spacing: 3px; font-weight: 900; 
            text-transform: uppercase; color: #fff; text-shadow: 0 0 15px rgba(0, 229, 255, 0.3); 
        }
        .header h1 span { color: var(--accent-blue); }
        .header-brand { font-size: 0.8rem; color: var(--text-muted); letter-spacing: 5px; margin-top: 5px; display: block;}

        .preset-panel { display: flex; gap: 10px; background: #000; padding: 10px 15px; border-radius: var(--radius); border: 1px solid var(--border);}
        .preset-panel select { padding: 8px 12px; background: #1a1b20; border: 1px solid var(--border); color: var(--accent-blue); border-radius: 4px; outline: none; font-family: monospace; font-size: 14px;}
        .preset-panel button { padding: 8px 18px; font-size: 0.85rem; font-weight: 600; text-transform: uppercase; letter-spacing: 1px; border-radius: 4px; transition: all 0.2s;}
        
        .btn-save { background: rgba(0, 255, 65, 0.1); border: 1px solid rgba(0, 255, 65, 0.3); color: var(--led-green); cursor: pointer; }
        .btn-save:hover { background: rgba(0, 255, 65, 0.2); box-shadow: 0 0 10px rgba(0, 255, 65, 0.2); }
        .btn-delete { background: rgba(255, 0, 60, 0.1); border: 1px solid rgba(255, 0, 60, 0.3); color: var(--led-red); cursor: pointer; }
        .btn-delete:hover { background: rgba(255, 0, 60, 0.2); box-shadow: 0 0 10px rgba(255, 0, 60, 0.2); }

        .module-box { background: #0c0d10; border: 1px solid var(--border); border-radius: var(--radius); padding: 18px; position: relative; }
        .module-title { font-size: 0.75rem; color: var(--text-muted); text-transform: uppercase; letter-spacing: 2px; margin-bottom: 15px; font-weight: 600; display: flex; justify-content: space-between; align-items: center;}
        
        .top-grid { display: grid; grid-template-columns: 1.2fr 1fr 2fr; gap: 20px; }
        @media (max-width: 1200px) { .top-grid { grid-template-columns: 1fr 1fr; } .top-grid > div:last-child { grid-column: 1 / -1; } }

        .input-row { display: flex; gap: 10px; width: 100%; margin-bottom: 12px;}
        input[type="file"] { flex: 1; padding: 10px; background: #16181d; border: 1px solid var(--border); color: var(--text-main); font-size: 0.85rem; border-radius: 4px; outline: none; }
        
        button { background: #1f2229; border: 1px solid var(--border); color: var(--text-main); border-radius: 4px; cursor: pointer; transition: 0.2s; padding: 10px; font-weight: 500;}
        button:hover { background: #2a2e37; }
        
        .btn-live { background: rgba(255, 170, 0, 0.1); border-color: rgba(255, 170, 0, 0.3); color: #ffaa00; font-weight: bold;}
        .btn-live.active { background: #ffaa00; color: #000; box-shadow: 0 0 15px rgba(255, 170, 0, 0.4); }
        
        canvas { width: 100%; background: #050505; border-radius: 4px; border: 1px solid #1a1c23; }
        #visualizer { height: 100px; margin-bottom: 12px; }
        .xover-canvas { height: 160px; } 
        .peq-canvas { height: 100px; margin-bottom: 15px; background: #060708; border-color: #1a1c23;}

        .transport-controls { display: flex; gap: 8px; }
        .transport-controls button { font-size: 0.8rem; font-weight: bold; flex: 1;}
        .transport-controls button.active { background: var(--accent-blue); color: #000; border-color: var(--accent-blue); box-shadow: 0 0 10px rgba(0, 229, 255, 0.3);}

        .gen-controls { display: flex; flex-direction: column; gap: 12px; }
        .gen-active { background: rgba(255, 42, 85, 0.1); border-color: rgba(255, 42, 85, 0.3); color: var(--accent-red); font-weight: bold;}
        .gen-active.active { background: var(--accent-red); color: #fff; box-shadow: 0 0 15px rgba(255, 42, 85, 0.4); border-color: var(--accent-red);}

        .dsp-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 25px; margin-top: 25px; }
        @media (max-width: 1400px) { .dsp-grid { grid-template-columns: 1fr; } }

        .channel-panel { background: #0d0e11; border: 1px solid var(--border); border-radius: var(--radius); padding: 25px; position: relative; overflow: hidden; }
        
        .ch-a-accent { position: absolute; top: 0; left: 0; width: 4px; height: 100%; background: var(--accent-blue); box-shadow: 0 0 15px var(--accent-blue);}
        .ch-b-accent { position: absolute; top: 0; left: 0; width: 4px; height: 100%; background: var(--accent-red); box-shadow: 0 0 15px var(--accent-red);}

        .channel-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px solid var(--border); padding-bottom: 15px; margin-bottom: 20px; margin-left: 10px;}
        .channel-title { font-size: 1.5rem; color: #fff; font-weight: 800; letter-spacing: 2px; }
        
        .meter-container { display: flex; gap: 4px; background: #000; padding: 6px; border: 1px solid #1a1c23; border-radius: 4px; height: 35px; align-items: flex-end;}
        .meter-led { width: 8px; height: 100%; background: #111; border-radius: 1px; transition: background-color 0.05s;}

        .channel-extras { display: flex; gap: 10px; }
        .btn-mute { background: rgba(255,0,0,0.1); color: var(--led-red); border: 1px solid rgba(255,0,0,0.3); padding: 8px 15px; font-weight: bold; font-size: 0.8rem;}
        .btn-mute.active { background: var(--led-red); color: #fff; box-shadow: 0 0 15px var(--led-red); border-color: var(--led-red);}
        .btn-phase { background: #1a1c23; color: #aaa; border: 1px solid var(--border); padding: 8px 15px; font-weight: bold; font-size: 0.8rem;}
        .btn-phase.active { background: #fff; color: #000; box-shadow: 0 0 10px #fff; border-color: #fff;}

        .control-row { display: grid; grid-template-columns: repeat(6, 1fr); gap: 12px; margin-bottom: 25px; margin-left: 10px;}

        .knob-group { display: flex; flex-direction: column; align-items: center; background: #14151a; border: 1px solid var(--border); padding: 15px 8px; border-radius: 6px; width: 100%;}
        .knob-group label { font-size: 0.6rem; color: var(--text-muted); margin-bottom: 12px; text-align: center; font-weight: 700; letter-spacing: 1px; text-transform: uppercase;}
        .knob-group span { font-family: 'JetBrains Mono', monospace; font-size: 0.75rem; color: #fff; margin-top: 12px; background: #000; padding: 4px 8px; border: 1px solid #222; border-radius: 4px;}
        
        .range-wrapper { display: flex; width: 100%; align-items: center; gap: 6px; }
        .range-wrapper input[type="range"] { flex: 1; width: 100%; accent-color: var(--accent-blue); height: 4px; background: #2d3139; outline: none; -webkit-appearance: none; border-radius: 2px;}
        .range-wrapper input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 14px; height: 20px; background: #fff; cursor: pointer; border-radius: 3px; border: 1px solid #000; box-shadow: 0 0 5px rgba(0,0,0,0.5);}
        .ch-b-accent ~ .control-row .range-wrapper input[type="range"] { accent-color: var(--accent-red); }

        .btn-adjust { flex: none; background: #2d3139; color: #fff; border: none; width: 22px; height: 22px; display: flex; justify-content: center; align-items: center; font-weight: bold; border-radius: 4px; padding: 0; font-size: 0.8rem;}
        .btn-adjust:hover { background: #4a5160; }

        .filter-select { width: 100%; background: #000; color: #fff; border: 1px solid #333; padding: 6px; font-size: 0.65rem; margin-bottom: 12px; outline: none; text-align: center; font-family: monospace; border-radius: 4px;}
        
        .peq-section { background: #101115; border: 1px solid var(--border); padding: 20px; border-radius: var(--radius); margin-bottom: 25px; margin-left: 10px;}
        .peq-grid { display: grid; grid-template-columns: repeat(5, 1fr); gap: 12px; }
        .peq-band { background: #16181d; padding: 12px; border: 1px solid #252830; border-radius: var(--radius); display: flex; flex-direction: column; gap: 10px; }
        .peq-band-title { font-size: 0.75rem; color: #fff; text-align: center; border-bottom: 1px solid #2a2d35; padding-bottom: 8px; margin-bottom: 5px; font-weight: bold;}
        
        .peq-control { display: flex; flex-direction: column; gap: 6px;}
        .peq-control-header { display: flex; justify-content: space-between; font-size: 0.65rem; color: var(--text-muted); font-weight: 600;}
        .peq-control-header span { color: #fff; font-family: monospace;}

        .eq-container { display: flex; justify-content: space-between; background: #101115; padding: 25px 15px; border-radius: var(--radius); border: 1px solid var(--border); overflow-x: auto; gap: 8px; margin-left: 10px;}
        .eq-band { display: flex; flex-direction: column; align-items: center; width: 35px; }
        .eq-band span.val { font-size: 0.7rem; color: #fff; margin-bottom: 8px; font-family: monospace; background: #000; padding: 2px 4px; border-radius: 3px; border: 1px solid #222;}
        .eq-band .eq-btn { width: 26px; height: 26px; font-size: 16px; padding: 0; display: flex; align-items: center; justify-content: center; background: #2d3139; color: #fff; border: none; border-radius: 4px; cursor: pointer;}
        .eq-band .eq-btn:hover { background: #4a5160;}
        .eq-band input[type="range"] { -webkit-appearance: slider-vertical; writing-mode: bt-lr; width: 6px; height: 120px; background: #2d3139; outline: none; border-radius: 3px; margin: 12px 0; accent-color: var(--accent-blue);}
        .ch-b-accent ~ .eq-container .eq-band input[type="range"] { accent-color: var(--accent-red); }
        .eq-band input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 20px; height: 8px; background: #fff; cursor: pointer; border-radius: 2px; border: 1px solid #000; box-shadow: 0 0 5px rgba(0,0,0,0.5);}
        .eq-band span.freq { font-size: 0.7rem; color: var(--text-muted); margin-top: 8px; font-family: monospace; font-weight: bold;}
    </style>
</head>
<body>

<div class="container">
    <div class="panel">
        <div class="header">
            <div>
                <h1>WU <span>AUDIO</span> DLMS PRO v8</h1>
                <span class="header-brand">WAHYU AUDIO PROJECT - ATS ANDRE TEKNIK SOLUTION</span>
            </div>
            <div class="preset-panel">
                <select id="preset-selector"><option value="">-- Load Preset Rack --</option></select>
                <button class="btn-save" onclick="savePreset()">SAVE</button>
                <button class="btn-delete" onclick="deletePreset()">DEL</button>
            </div>
        </div>

        <div class="top-grid">
            <div class="module-box">
                <div class="module-title">Main Input Source</div>
                <div class="input-row">
                    <input type="file" id="audio-upload" accept="audio/*" multiple title="Upload file audio (MP3, WAV)">
                </div>
                <button id="btn-live-input" class="btn-live" style="width: 100%; margin-top: 5px;">🎙️ Enable Live Line-In / Mic</button>
            </div>

            <div class="module-box">
                <div class="module-title">Frequency Generator (Oscillator)</div>
                <div class="gen-controls">
                    <button id="btn-noise-toggle" class="gen-active" style="width: 100%;">TEMBAK FREKUENSI (OFF)</button>
                    <div style="display: flex; gap: 10px; width: 100%;">
                        <select id="noise-route" class="filter-select" style="margin:0; flex:1;">
                            <option value="both">Routing: L + R</option>
                            <option value="left">Routing: CH A (L)</option>
                            <option value="right">Routing: CH B (R)</option>
                        </select>
                        <select id="osc-type" class="filter-select" style="margin:0; flex:1;">
                            <option value="sine" selected>Sine Wave (Clean)</option>
                            <option value="square">Square</option>
                            <option value="sawtooth">Sawtooth</option>
                        </select>
                    </div>
                    <div class="range-wrapper" style="margin-top: 5px;">
                        <span style="font-size: 0.7rem; font-family: monospace; color:var(--text-muted);">FREQ</span>
                        <input type="range" id="noise-freq" min="20" max="20000" step="1" value="1000">
                        <span id="val-noise-freq" style="font-size: 0.8rem; font-family: monospace; font-weight: bold; width: 60px; text-align: right;">1000 Hz</span>
                    </div>
                </div>
            </div>

            <div class="module-box" style="display: flex; flex-direction: column;">
                <div class="module-title">
                    <span>RTA Master Out</span>
                    <div class="range-wrapper" style="width: 180px;">
                        <span style="font-size: 0.7rem; color: var(--text-muted);">MASTER VOL</span>
                        <input type="range" id="vol-slider" min="0" max="1.5" step="0.01" value="0.8">
                        <span id="vol-val" style="font-size: 0.75rem; font-family: monospace; color: #fff;">80%</span>
                    </div>
                </div>
                <canvas id="visualizer"></canvas>
                <div class="transport-controls">
                    <button id="btn-prev">|&#9664;</button>
                    <button id="btn-play" style="flex: 2;">PLAY / PAUSE</button>
                    <button id="btn-stop">&#9632;</button>
                    <button id="btn-next">&#9654;|</button>
                </div>
            </div>

            <div class="module-box master-crossover-container" style="grid-column: 1 / -1;">
                <div class="module-title">
                    <span>Master Crossover Response</span>
                    <span style="font-size: 0.8rem; letter-spacing: normal;">
                        <span style="color:var(--accent-blue);">CH A (LEFT)</span> &nbsp;|&nbsp; <span style="color:var(--accent-red);">CH B (RIGHT)</span>
                    </span>
                </div>
                <canvas id="xover-master" class="xover-canvas"></canvas>
            </div>
        </div>

        <div class="dsp-grid">
            <div class="channel-panel">
                <div class="ch-a-accent"></div>
                <div class="channel-header">
                    <div class="channel-title" style="color: var(--accent-blue);">CH A (LEFT)</div>
                    <div style="display: flex; gap: 15px; align-items: center;">
                        <div class="meter-container" id="meter-l"></div>
                        <div class="channel-extras">
                            <button id="mute-l" class="btn-mute">MUTE</button>
                            <button id="phase-l" class="btn-phase">INV Phase (180&deg;)</button>
                        </div>
                    </div>
                </div>
                
                <div class="control-row">
                    <div class="knob-group">
                        <label>Out Gain (dB)</label>
                        <div style="height: 25px;"></div>
                        <div class="range-wrapper">
                            <button class="btn-adjust" onclick="adjustValue('gain-l', -0.1)">-</button>
                            <input type="range" id="gain-l" min="-24" max="12" step="0.1" value="0" data-preset="true">
                            <button class="btn-adjust" onclick="adjustValue('gain-l', 0.1)">+</button>
                        </div>
                        <span id="val-gain-l">0.0 dB</span>
                    </div>
                    <div class="knob-group">
                        <label>Brick Limiter</label>
                        <div style="height: 25px;"></div>
                        <div class="range-wrapper">
                            <button class="btn-adjust" onclick="adjustValue('limit-l', -1)">-</button>
                            <input type="range" id="limit-l" min="-40" max="0" step="1" value="-2" data-preset="true">
                            <button class="btn-adjust" onclick="adjustValue('limit-l', 1)">+</button>
                        </div>
                        <span id="val-limit-l">-2 dB</span>
                    </div>
                    <div class="knob-group">
                        <label>HPF (Cut Low)</label>
                        <select id="hpf-type-l" class="filter-select" data-preset="true">
                            <option value="BW12">Butterworth 12</option>
                            <option value="BW24">Butterworth 24</option>
                            <option value="LR24">Linkwitz 24</option>
                            <option value="LR48" selected>Linkwitz 48</option>
                        </select>
                        <div class="range-wrapper">
                            <button class="btn-adjust" onclick="adjustValue('hpf-l', -1)">-</button>
                            <input type="range" id="hpf-l" min="20" max="20000" step="1" value="40" data-preset="true">
                            <button class="btn-adjust" onclick="adjustValue('hpf-l', 1)">+</button>
                        </div>
                        <span id="val-hpf-l">40 Hz</span>
                    </div>
                    <div class="knob-group">
                        <label>LPF (Cut High)</label>
                        <select id="lpf-type-l" class="filter-select" data-preset="true">
                            <option value="BW12">Butterworth 12</option>
                            <option value="BW24">Butterworth 24</option>
                            <option value="LR24">Linkwitz 24</option>
                            <option value="LR48" selected>Linkwitz 48</option>
                        </select>
                        <div class="range-wrapper">
                            <button class="btn-adjust" onclick="adjustValue('lpf-l', -10)">-</button>
                            <input type="range" id="lpf-l"
