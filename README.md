<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Advanced Quiz Video Generator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/RecordRTC/5.6.2/RecordRTC.min.js"></script>
    <script src="https://html2canvas.hertzen.com/dist/html2canvas.min.js"></script>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            padding: 10px;
            background-color: #f5f5f5;
            color: #333;
            overflow-x: hidden;
            width: 100%;
            min-width: 320px;
        }
        
        .container {
            max-width: 100%;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        
        h1, h2 {
            text-align: center;
            color: #2c3e50;
            margin-bottom: 10px;
            word-wrap: break-word;
        }
        
        h1 {
            font-size: 1.5rem;
        }
        
        h2 {
            font-size: 1.2rem;
            margin-top: 5px;
        }
        
        .section {
            background-color: white;
            padding: 12px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            width: 100%;
        }
        
        textarea {
            width: 100%;
            min-height: 120px;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-family: monospace;
            resize: vertical;
            font-size: 14px;
        }
        
        .preview-container {
            position: relative;
            margin: 10px auto;
            overflow: hidden;
            background-size: cover;
            background-position: center;
            width: 100%;
            aspect-ratio: 16/9;
            border: 1px solid #ddd;
        }
        
        .video-frame {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            gap: 15px;
        }
        
        .question-box, .answer-box {
            margin: 8px;
            padding: 12px;
            border-radius: 8px;
            text-align: center;
            max-width: 90%;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            transition: all 0.5s ease;
            position: relative;
            width: 80%;
            line-height: 1.5;
            word-wrap: break-word;
        }
        
        .question-box {
            background-color: #3498db;
            color: white;
            opacity: 0;
        }
        
        .question-box.visible {
            opacity: 1;
        }
        
        .answer-box {
            background-color: #2ecc71;
            color: white;
            opacity: 0;
            display: none;
        }
        
        .answer-box.visible {
            opacity: 1;
            display: block;
        }
        
        /* Question Transition Animations */
        .question-slide-up {
            animation: questionSlideUp 0.5s ease-out forwards;
        }
        
        .question-slide-down {
            animation: questionSlideDown 0.5s ease-out forwards;
        }
        
        .question-slide-left {
            animation: questionSlideLeft 0.5s ease-out forwards;
        }
        
        .question-slide-right {
            animation: questionSlideRight 0.5s ease-out forwards;
        }
        
        .question-zoom-in {
            animation: questionZoomIn 0.5s ease-out forwards;
        }
        
        .question-rotate-in {
            animation: questionRotateIn 0.5s ease-out forwards;
        }
        
        .question-bounce-in {
            animation: questionBounceIn 0.75s ease-out forwards;
        }
        
        .question-fade-in {
            animation: questionFadeIn 0.5s ease-out forwards;
        }
        
        .question-flip-in {
            animation: questionFlipIn 0.7s ease-out forwards;
        }
        
        /* Answer Transition Animations */
        .answer-slide-up {
            animation: answerSlideUp 0.5s ease-out forwards;
        }
        
        .answer-slide-down {
            animation: answerSlideDown 0.5s ease-out forwards;
        }
        
        .answer-slide-left {
            animation: answerSlideLeft 0.5s ease-out forwards;
        }
        
        .answer-slide-right {
            animation: answerSlideRight 0.5s ease-out forwards;
        }
        
        .answer-zoom-in {
            animation: answerZoomIn 0.5s ease-out forwards;
        }
        
        .answer-rotate-in {
            animation: answerRotateIn 0.5s ease-out forwards;
        }
        
        .answer-bounce-in {
            animation: answerBounceIn 0.75s ease-out forwards;
        }
        
        .answer-fade-in {
            animation: answerFadeIn 0.5s ease-out forwards;
        }
        
        .answer-flip-in {
            animation: answerFlipIn 0.7s ease-out forwards;
        }
        
        /* Keyframes for Question Animations */
        @keyframes questionSlideUp {
            0% { opacity: 0; transform: translateY(50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes questionSlideDown {
            0% { opacity: 0; transform: translateY(-50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes questionSlideLeft {
            0% { opacity: 0; transform: translateX(50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        
        @keyframes questionSlideRight {
            0% { opacity: 0; transform: translateX(-50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        
        @keyframes questionZoomIn {
            0% { opacity: 0; transform: scale(0.5); }
            100% { opacity: 1; transform: scale(1); }
        }
        
        @keyframes questionRotateIn {
            0% { opacity: 0; transform: rotate(-180deg) scale(0.5); }
            100% { opacity: 1; transform: rotate(0) scale(1); }
        }
        
        @keyframes questionBounceIn {
            0% { opacity: 0; transform: scale(0.3); }
            50% { opacity: 1; transform: scale(1.1); }
            70% { transform: scale(0.9); }
            100% { opacity: 1; transform: scale(1); }
        }
        
        @keyframes questionFadeIn {
            0% { opacity: 0; }
            100% { opacity: 1; }
        }
        
        @keyframes questionFlipIn {
            0% { opacity: 0; transform: perspective(400px) rotateY(90deg); }
            40% { transform: perspective(400px) rotateY(-20deg); }
            70% { transform: perspective(400px) rotateY(10deg); }
            100% { opacity: 1; transform: perspective(400px) rotateY(0); }
        }
        
        /* Keyframes for Answer Animations */
        @keyframes answerSlideUp {
            0% { opacity: 0; transform: translateY(50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes answerSlideDown {
            0% { opacity: 0; transform: translateY(-50px); }
            100% { opacity: 1; transform: translateY(0); }
        }
        
        @keyframes answerSlideLeft {
            0% { opacity: 0; transform: translateX(50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        
        @keyframes answerSlideRight {
            0% { opacity: 0; transform: translateX(-50px); }
            100% { opacity: 1; transform: translateX(0); }
        }
        
        @keyframes answerZoomIn {
            0% { opacity: 0; transform: scale(0.5); }
            100% { opacity: 1; transform: scale(1); }
        }
        
        @keyframes answerRotateIn {
            0% { opacity: 0; transform: rotate(-180deg) scale(0.5); }
            100% { opacity: 1; transform: rotate(0) scale(1); }
        }
        
        @keyframes answerBounceIn {
            0% { opacity: 0; transform: scale(0.3); }
            50% { opacity: 1; transform: scale(1.1); }
            70% { transform: scale(0.9); }
            100% { opacity: 1; transform: scale(1); }
        }
        
        @keyframes answerFadeIn {
            0% { opacity: 0; }
            100% { opacity: 1; }
        }
        
        @keyframes answerFlipIn {
            0% { opacity: 0; transform: perspective(400px) rotateY(90deg); }
            40% { transform: perspective(400px) rotateY(-20deg); }
            70% { transform: perspective(400px) rotateY(10deg); }
            100% { opacity: 1; transform: perspective(400px) rotateY(0); }
        }
        
        .countdown {
            font-size: 1.8rem;
            font-weight: bold;
            color: #e74c3c;
            position: absolute;
            opacity: 0;
            transition: opacity 0.3s ease;
        }
        
        .countdown.visible {
            opacity: 1;
        }
        
        .controls {
            display: grid;
            grid-template-columns: 1fr;
            gap: 12px;
            margin-bottom: 12px;
        }
        
        .control-group {
            display: flex;
            flex-direction: column;
        }
        
        .control-group h3 {
            margin: 8px 0 4px;
            padding-bottom: 4px;
            border-bottom: 1px solid #eee;
            font-size: 0.95rem;
            color: #2c3e50;
        }
        
        label {
            margin-bottom: 4px;
            font-weight: bold;
            font-size: 0.85rem;
        }
        
        select, input, button {
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            margin-bottom: 6px;
            font-size: 0.85rem;
            width: 100%;
        }
        
        button {
            background-color: #3498db;
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
            padding: 10px;
        }
        
        button:hover {
            background-color: #2980b9;
        }
        
        /* Font size controls */
        .font-controls {
            display: flex;
            gap: 8px;
            margin-top: 8px;
            flex-wrap: wrap;
        }
        
        .font-control-group {
            flex: 1;
            min-width: 100px;
        }
        
        .font-size-controls {
            display: flex;
            gap: 8px;
            align-items: center;
            margin-top: 8px;
        }
        
        .font-size-btn {
            width: 35px;
            height: 35px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            background-color: #3498db;
            color: white;
            border-radius: 4px;
            cursor: pointer;
        }
        
        .font-size-value {
            min-width: 35px;
            text-align: center;
            font-weight: bold;
        }
        
        .line-height-controls {
            display: flex;
            gap: 8px;
            align-items: center;
            margin-top: 8px;
        }
        
        .line-height-value {
            min-width: 35px;
            text-align: center;
            font-weight: bold;
        }
        
        /* Transition effects */
        .transition-select {
            margin-top: 8px;
        }
        
        /* Branding styles */
        .branding-container {
            position: absolute;
            z-index: 10;
            max-width: 30%;
        }
        
        .branding-logo {
            max-width: 100%;
            max-height: 80px;
        }
        
        .branding-text {
            color: white;
            text-shadow: 1px 1px 3px rgba(0,0,0,0.8);
            padding: 4px 8px;
            border-radius: 4px;
            background-color: rgba(0,0,0,0.3);
            font-size: 14px;
        }
        
        /* Audio controls */
        .audio-controls {
            display: flex;
            gap: 8px;
            align-items: center;
            margin-top: 8px;
        }
        
        .audio-volume {
            flex-grow: 1;
        }
        
        /* Recording controls */
        .recording-controls {
            display: flex;
            gap: 8px;
            margin-top: 12px;
            flex-wrap: wrap;
            justify-content: center;
        }
        
        .recording-btn {
            background-color: #e74c3c;
            color: white;
            padding: 8px 12px;
            border-radius: 4px;
            border: none;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
            font-size: 0.85rem;
        }
        
        .recording-btn:hover {
            background-color: #c0392b;
        }
        
        .recording-btn.start {
            background-color: #2ecc71;
        }
        
        .recording-btn.start:hover {
            background-color: #27ae60;
        }
        
        .recording-btn:disabled {
            background-color: #95a5a6;
            cursor: not-allowed;
        }
        
        .recording-status {
            display: flex;
            align-items: center;
            gap: 4px;
            font-weight: bold;
            color: #e74c3c;
            font-size: 0.85rem;
        }
        
        .recording-indicator {
            width: 10px;
            height: 10px;
            background-color: #e74c3c;
            border-radius: 50%;
            animation: pulse 1.5s infinite;
        }
        
        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }
        
        /* Recording preview */
        .recording-preview-container {
            margin-top: 15px;
            border: 2px dashed #ccc;
            padding: 8px;
            border-radius: 8px;
        }
        
        .recording-preview-video {
            width: 100%;
            max-height: 250px;
            background-color: #000;
        }
        
        .recording-preview-controls {
            display: flex;
            gap: 8px;
            margin-top: 8px;
            justify-content: center;
            flex-wrap: wrap;
        }
        
        .recording-time {
            font-weight: bold;
            text-align: center;
            margin-top: 4px;
            font-size: 0.85rem;
        }
        
        /* Video element styles */
        #bg-video-element {
            position: absolute;
            width: 100%;
            height: 100%;
            object-fit: cover;
            display: none;
        }
        
        /* Responsive adjustments */
        @media (min-width: 768px) {
            .controls {
                grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            }
            
            h1 {
                font-size: 1.8rem;
            }
            
            h2 {
                font-size: 1.3rem;
            }
            
            .section {
                padding: 15px;
            }
            
            .question-box, .answer-box {
                padding: 15px;
            }
            
            .countdown {
                font-size: 2rem;
            }
            
            .recording-btn {
                padding: 10px 15px;
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Advanced Quiz Video Generator</h1>
        
        <div class="section">
            <h2>Enter Questions and Answers</h2>
            <p>Format: One question and answer per line, separated by "|"</p>
            <textarea id="qa-input" placeholder="What is the capital of France?|Paris
How many continents are there?|7
What is 2+2?|4"></textarea>
        </div>
        
        <div class="section">
            <h2>Video Settings</h2>
            <div class="controls">
                <div class="control-group">
                    <h3>Layout Settings</h3>
                    <label for="aspect-ratio">Aspect Ratio</label>
                    <select id="aspect-ratio">
                        <option value="16/9">Horizontal (16:9)</option>
                        <option value="9/16">Vertical (9:16)</option>
                        <option value="1/1">Square (1:1)</option>
                    </select>
                    
                    <label for="layout">Layout</label>
                    <select id="layout">
                        <option value="stacked">Stacked (Vertical)</option>
                        <option value="side-by-side">Side by Side (Horizontal)</option>
                    </select>
                    
                    <label for="question-time">Question Time (seconds)</label>
                    <input type="number" id="question-time" min="1" max="60" value="5">
                    
                    <label for="answer-time">Answer Time (seconds)</label>
                    <input type="number" id="answer-time" min="1" max="60" value="5">
                    
                    <label for="countdown-time">Countdown Time (seconds)</label>
                    <input type="number" id="countdown-time" min="1" max="10" value="3">
                </div>
                
                <div class="control-group">
                    <h3>Background Settings</h3>
                    <label for="bg-type">Background</label>
                    <select id="bg-type">
                        <option value="color">Color</option>
                        <option value="image">Image</option>
                        <option value="gradient">Gradient</option>
                        <option value="video">Video</option>
                    </select>
                    
                    <div id="bg-color-control">
                        <label for="bg-color">Background Color</label>
                        <input type="color" id="bg-color" value="#ffffff">
                    </div>
                    
                    <div id="bg-image-control" style="display:none">
                        <label>Background Image</label>
                        <input type="file" id="bg-image" accept="image/*">
                    </div>
                    
                    <div id="bg-video-control" style="display:none">
                        <label>Background Video</label>
                        <input type="file" id="bg-video" accept="video/mp4,video/webm,video/ogg">
                        <label for="video-volume">Video Volume</label>
                        <input type="range" id="video-volume" min="0" max="1" step="0.1" value="0.5">
                    </div>
                    
                    <div id="bg-gradient-control" style="display:none">
                        <label for="gradient-color1">Gradient Color 1</label>
                        <input type="color" id="gradient-color1" value="#3498db">
                        <label for="gradient-color2">Gradient Color 2</label>
                        <input type="color" id="gradient-color2" value="#2ecc71">
                        <label for="gradient-direction">Gradient Direction</label>
                        <select id="gradient-direction">
                            <option value="to right">Horizontal</option>
                            <option value="to bottom">Vertical</option>
                            <option value="to bottom right">Diagonal</option>
                            <option value="circle">Radial</option>
                        </select>
                    </div>
                </div>
                
                <div class="control-group">
                    <h3>Box Appearance</h3>
                    <label for="question-color">Question Box Color</label>
                    <input type="color" id="question-color" value="#3498db">
                    
                    <label for="answer-color">Answer Box Color</label>
                    <input type="color" id="answer-color" value="#2ecc71">
                    
                    <label for="text-color">Text Color</label>
                    <input type="color" id="text-color" value="#ffffff">
                    
                    <label for="box-opacity">Box Opacity</label>
                    <input type="range" id="box-opacity" min="0.5" max="1" step="0.1" value="1">
                    
                    <label for="box-border">Box Border</label>
                    <input type="number" id="box-border" min="0" max="10" value="0">
                </div>
                
                <div class="control-group">
                    <h3>Text Settings</h3>
                    <label>Font Family</label>
                    <select id="font-family">
                        <option value="Arial, sans-serif">Arial</option>
                        <option value="'Helvetica Neue', sans-serif">Helvetica</option>
                        <option value="'Times New Roman', serif">Times New Roman</option>
                        <option value="Georgia, serif">Georgia</option>
                        <option value="'Courier New', monospace">Courier New</option>
                        <option value="'Palatino Linotype', serif">Palatino</option>
                        <option value="'Gill Sans', sans-serif">Gill Sans</option>
                        <option value="'Trebuchet MS', sans-serif">Trebuchet</option>
                        <option value="Verdana, sans-serif">Verdana</option>
                        <option value="'Impact', sans-serif">Impact</option>
                        <option value="'Comic Sans MS', cursive">Comic Sans</option>
                        <option value="'Brush Script MT', cursive">Brush Script</option>
                    </select>
                    
                    <label>Text Size</label>
                    <div class="font-size-controls">
                        <button id="font-decrease" class="font-size-btn">-</button>
                        <div id="font-size-value" class="font-size-value">1.2rem</div>
                        <button id="font-increase" class="font-size-btn">+</button>
                    </div>
                    
                    <label>Question Line Height</label>
                    <div class="line-height-controls">
                        <button id="q-line-decrease" class="font-size-btn">-</button>
                        <div id="q-line-value" class="line-height-value">1.5</div>
                        <button id="q-line-increase" class="font-size-btn">+</button>
                    </div>
                    
                    <label>Answer Line Height</label>
                    <div class="line-height-controls">
                        <button id="a-line-decrease" class="font-size-btn">-</button>
                        <div id="a-line-value" class="line-height-value">1.5</div>
                        <button id="a-line-increase" class="font-size-btn">+</button>
                    </div>
                    
                    <label for="text-shadow">Text Shadow</label>
                    <input type="checkbox" id="text-shadow" checked>
                </div>
                
                <div class="control-group">
                    <h3>Animation & Effects</h3>
                    <label for="question-transition">Question Transition</label>
                    <select id="question-transition" class="transition-select">
                        <option value="slide-up">Slide Up</option>
                        <option value="slide-down">Slide Down</option>
                        <option value="slide-left">Slide Left</option>
                        <option value="slide-right">Slide Right</option>
                        <option value="zoom-in">Zoom In</option>
                        <option value="rotate-in">Rotate In</option>
                        <option value="bounce-in">Bounce In</option>
                        <option value="fade-in">Fade In</option>
                        <option value="flip-in">Flip In</option>
                    </select>
                    
                    <label for="answer-transition">Answer Transition</label>
                    <select id="answer-transition" class="transition-select">
                        <option value="slide-up">Slide Up</option>
                        <option value="slide-down">Slide Down</option>
                        <option value="slide-left">Slide Left</option>
                        <option value="slide-right">Slide Right</option>
                        <option value="zoom-in">Zoom In</option>
                        <option value="rotate-in">Rotate In</option>
                        <option value="bounce-in">Bounce In</option>
                        <option value="fade-in">Fade In</option>
                        <option value="flip-in">Flip In</option>
                    </select>
                    
                    <label for="countdown-enabled">Show Countdown</label>
                    <input type="checkbox" id="countdown-enabled" checked>
                    
                    <label for="countdown-position">Countdown Position</label>
                    <select id="countdown-position">
                        <option value="below">Below Question</option>
                        <option value="top-right">Top Right</option>
                        <option value="top-left">Top Left</option>
                    </select>
                    
                    <label for="countdown-color">Countdown Color</label>
                    <input type="color" id="countdown-color" value="#e74c3c">
                </div>
                
                <div class="control-group">
                    <h3>Audio Settings</h3>
                    <label for="bg-audio">Background Audio</label>
                    <input type="file" id="bg-audio" accept="audio/mpeg,audio/wav,audio/ogg">
                    
                    <div class="audio-controls">
                        <label for="audio-volume">Audio Volume</label>
                        <input type="range" id="audio-volume" class="audio-volume" min="0" max="1" step="0.1" value="0.5">
                    </div>
                    
                    <label for="audio-loop">Loop Audio</label>
                    <input type="checkbox" id="audio-loop" checked>
                </div>
                
                <div class="control-group">
                    <h3>Branding</h3>
                    <label for="branding-type">Branding Type</label>
                    <select id="branding-type">
                        <option value="none">None</option>
                        <option value="logo">Logo</option>
                        <option value="text">Text</option>
                    </select>
                    
                    <div id="branding-logo-control" style="display:none">
                        <label>Logo Image</label>
                        <input type="file" id="branding-logo" accept="image/*">
                        
                        <label for="logo-position">Logo Position</label>
                        <select id="logo-position">
                            <option value="top-left">Top Left</option>
                            <option value="top-right">Top Right</option>
                            <option value="bottom-left">Bottom Left</option>
                            <option value="bottom-right">Bottom Right</option>
                        </select>
                        
                        <label for="logo-size">Logo Size (%)</label>
                        <input type="range" id="logo-size" min="5" max="30" value="15">
                    </div>
                    
                    <div id="branding-text-control" style="display:none">
                        <label>Brand Text</label>
                        <input type="text" id="branding-text" placeholder="Enter your brand name">
                        
                        <label for="text-font">Text Font</label>
                        <select id="text-font">
                            <option value="Arial, sans-serif">Arial</option>
                            <option value="'Helvetica Neue', sans-serif">Helvetica</option>
                            <option value="'Times New Roman', serif">Times New Roman</option>
                            <option value="Georgia, serif">Georgia</option>
                            <option value="'Courier New', monospace">Courier New</option>
                            <option value="'Palatino Linotype', serif">Palatino</option>
                            <option value="'Gill Sans', sans-serif">Gill Sans</option>
                            <option value="'Trebuchet MS', sans-serif">Trebuchet</option>
                            <option value="Verdana, sans-serif">Verdana</option>
                            <option value="'Impact', sans-serif">Impact</option>
                            <option value="'Comic Sans MS', cursive">Comic Sans</option>
                            <option value="'Brush Script MT', cursive">Brush Script</option>
                        </select>
                        
                        <label for="text-position">Text Position</label>
                        <select id="text-position">
                            <option value="top-left">Top Left</option>
                            <option value="top-right">Top Right</option>
                            <option value="bottom-left">Bottom Left</option>
                            <option value="bottom-right">Bottom Right</option>
                        </select>
                        
                        <label for="text-size">Text Size</label>
                        <input type="range" id="text-size" min="10" max="50" value="20">
                        
                        <label for="text-color">Text Color</label>
                        <input type="color" id="text-color" value="#ffffff">
                    </div>
                </div>
            </div>
            
            <div style="display: flex; gap: 8px; margin-top: 12px; flex-wrap: wrap;">
                <button id="preview-btn">Preview First QA</button>
                <button id="start-preview-btn">Start Full Preview</button>
                <button id="stop-preview-btn" disabled>Stop Preview</button>
            </div>
        </div>
        
        <div class="section">
            <h2>Preview</h2>
            <div class="preview-container" id="preview-container">
                <video id="bg-video-element" autoplay loop muted></video>
                <audio id="bg-audio-element" loop style="display: none;"></audio>
                <div class="video-frame" id="video-frame">
                    <div class="question-box" id="question-box"></div>
                    <div class="answer-box" id="answer-box"></div>
                    <div class="countdown" id="countdown"></div>
                    <div id="branding-element" class="branding-container" style="display: none;"></div>
                </div>
            </div>
            
            <div class="recording-controls">
                <button id="start-recording" class="recording-btn start">Start Recording</button>
                <button id="stop-recording" class="recording-btn" disabled>Stop Recording</button>
                <div id="recording-status" class="recording-status" style="display: none;">
                    <div class="recording-indicator"></div>
                    <span>Recording</span>
                    <span id="recording-timer">00:00</span>
                </div>
            </div>
        </div>
        
        <div class="section recording-preview-container" id="recording-preview-container" style="display: none;">
            <h2>Recording Preview</h2>
            <video id="recording-preview-video" class="recording-preview-video" controls></video>
            <div class="recording-time" id="recording-time">00:00</div>
            <div class="recording-preview-controls">
                <button id="play-recording-btn" class="recording-btn start">Play</button>
                <button id="pause-recording-btn" class="recording-btn">Pause</button>
                <button id="download-recording-btn" class="recording-btn">Download Video</button>
            </div>
        </div>
    </div>

    <script>
        // DOM Elements
        const elements = {
            qaInput: document.getElementById('qa-input'),
            aspectRatio: document.getElementById('aspect-ratio'),
            layout: document.getElementById('layout'),
            questionTime: document.getElementById('question-time'),
            answerTime: document.getElementById('answer-time'),
            countdownTime: document.getElementById('countdown-time'),
            bgType: document.getElementById('bg-type'),
            bgColor: document.getElementById('bg-color'),
            bgImage: document.getElementById('bg-image'),
            bgVideoInput: document.getElementById('bg-video'),
            bgVideoElement: document.getElementById('bg-video-element'),
            videoVolume: document.getElementById('video-volume'),
            gradientColor1: document.getElementById('gradient-color1'),
            gradientColor2: document.getElementById('gradient-color2'),
            gradientDirection: document.getElementById('gradient-direction'),
            questionColor: document.getElementById('question-color'),
            answerColor: document.getElementById('answer-color'),
            textColor: document.getElementById('text-color'),
            boxOpacity: document.getElementById('box-opacity'),
            boxBorder: document.getElementById('box-border'),
            fontFamily: document.getElementById('font-family'),
            countdownEnabled: document.getElementById('countdown-enabled'),
            countdownPosition: document.getElementById('countdown-position'),
            countdownColor: document.getElementById('countdown-color'),
            previewBtn: document.getElementById('preview-btn'),
            startPreviewBtn: document.getElementById('start-preview-btn'),
            stopPreviewBtn: document.getElementById('stop-preview-btn'),
            previewContainer: document.getElementById('preview-container'),
            videoFrame: document.getElementById('video-frame'),
            questionBox: document.getElementById('question-box'),
            answerBox: document.getElementById('answer-box'),
            countdown: document.getElementById('countdown'),
            bgColorControl: document.getElementById('bg-color-control'),
            bgImageControl: document.getElementById('bg-image-control'),
            bgVideoControl: document.getElementById('bg-video-control'),
            bgGradientControl: document.getElementById('bg-gradient-control'),
            questionTransition: document.getElementById('question-transition'),
            answerTransition: document.getElementById('answer-transition'),
            textShadow: document.getElementById('text-shadow'),
            fontDecreaseBtn: document.getElementById('font-decrease'),
            fontIncreaseBtn: document.getElementById('font-increase'),
            fontSizeValue: document.getElementById('font-size-value'),
            qLineDecreaseBtn: document.getElementById('q-line-decrease'),
            qLineIncreaseBtn: document.getElementById('q-line-increase'),
            qLineValue: document.getElementById('q-line-value'),
            aLineDecreaseBtn: document.getElementById('a-line-decrease'),
            aLineIncreaseBtn: document.getElementById('a-line-increase'),
            aLineValue: document.getElementById('a-line-value'),
            bgAudioInput: document.getElementById('bg-audio'),
            bgAudioElement: document.getElementById('bg-audio-element'),
            audioVolume: document.getElementById('audio-volume'),
            audioLoop: document.getElementById('audio-loop'),
            brandingType: document.getElementById('branding-type'),
            brandingLogoControl: document.getElementById('branding-logo-control'),
            brandingTextControl: document.getElementById('branding-text-control'),
            brandingLogoInput: document.getElementById('branding-logo'),
            logoPosition: document.getElementById('logo-position'),
            logoSize: document.getElementById('logo-size'),
            brandingTextInput: document.getElementById('branding-text'),
            textFont: document.getElementById('text-font'),
            textPosition: document.getElementById('text-position'),
            textSize: document.getElementById('text-size'),
            brandingTextColor: document.getElementById('text-color'),
            brandingElement: document.getElementById('branding-element'),
            startRecordingBtn: document.getElementById('start-recording'),
            stopRecordingBtn: document.getElementById('stop-recording'),
            recordingStatus: document.getElementById('recording-status'),
            recordingTimer: document.getElementById('recording-timer'),
            recordingPreviewContainer: document.getElementById('recording-preview-container'),
            recordingPreviewVideo: document.getElementById('recording-preview-video'),
            recordingTime: document.getElementById('recording-time'),
            playRecordingBtn: document.getElementById('play-recording-btn'),
            pauseRecordingBtn: document.getElementById('pause-recording-btn'),
            downloadRecordingBtn: document.getElementById('download-recording-btn')
        };

        // State
        let state = {
            qaPairs: [],
            isPreviewing: false,
            currentIndex: 0,
            backgroundImage: null,
            backgroundVideo: null,
            backgroundAudio: null,
            brandingLogo: null,
            previewInterval: null,
            countdownInterval: null,
            audioContext: null,
            currentFontSize: 1.2,
            questionLineHeight: 1.5,
            answerLineHeight: 1.5,
            actionQueue: [],
            isProcessingAction: false,
            recording: false,
            recorder: null,
            recordingStartTime: null,
            timerInterval: null,
            recordedBlob: null,
            recordingDuration: 0,
            isCapturingFrames: false,
            mediaStream: null
        };

        // Initialize
        function init() {
            // Check if RecordRTC is available
            if (typeof RecordRTC === 'undefined') {
                console.error("RecordRTC is not loaded. Recording functionality will not work.");
                elements.startRecordingBtn.disabled = true;
                elements.startRecordingBtn.title = "RecordRTC library not loaded";
            }
            
            setupEventListeners();
            updateBackgroundControls();
            updateBrandingControls();
            updatePreview();
            updateFontSizeDisplay();
            updateLineHeightDisplay();
            
            try {
                state.audioContext = new (window.AudioContext || window.webkitAudioContext)();
            } catch (e) {
                console.error("Web Audio API not supported");
            }
        }

        // Event Listeners
        function setupEventListeners() {
            // Background controls
            elements.bgType.addEventListener('change', updateBackgroundControls);
            elements.bgImage.addEventListener('change', handleImageUpload);
            elements.bgVideoInput.addEventListener('change', handleVideoUpload);
            elements.videoVolume.addEventListener('input', updateVideoVolume);
            elements.gradientDirection.addEventListener('change', updatePreview);
            
            // Audio controls
            elements.bgAudioInput.addEventListener('change', handleAudioUpload);
            elements.audioVolume.addEventListener('input', updateAudioVolume);
            elements.audioLoop.addEventListener('change', updateAudioLoop);
            
            // Branding controls
            elements.brandingType.addEventListener('change', updateBrandingControls);
            elements.brandingLogoInput.addEventListener('change', handleLogoUpload);
            elements.logoPosition.addEventListener('change', updateBrandingPosition);
            elements.logoSize.addEventListener('input', updateBrandingSize);
            elements.brandingTextInput.addEventListener('input', updateBrandingText);
            elements.textFont.addEventListener('change', updateBrandingText);
            elements.textPosition.addEventListener('change', updateBrandingPosition);
            elements.textSize.addEventListener('input', updateBrandingSize);
            elements.brandingTextColor.addEventListener('input', updateBrandingText);
            
            // Preview controls
            elements.previewBtn.addEventListener('click', previewFirstQA);
            elements.startPreviewBtn.addEventListener('click', startFullPreview);
            elements.stopPreviewBtn.addEventListener('click', stopFullPreview);
            
            // Font controls
            elements.fontDecreaseBtn.addEventListener('click', decreaseFontSize);
            elements.fontIncreaseBtn.addEventListener('click', increaseFontSize);
            elements.fontFamily.addEventListener('change', updateFontFamily);
            
            // Line height controls
            elements.qLineDecreaseBtn.addEventListener('click', () => changeLineHeight('question', -0.1));
            elements.qLineIncreaseBtn.addEventListener('click', () => changeLineHeight('question', 0.1));
            elements.aLineDecreaseBtn.addEventListener('click', () => changeLineHeight('answer', -0.1));
            elements.aLineIncreaseBtn.addEventListener('click', () => changeLineHeight('answer', 0.1));
            
            // Recording controls
            elements.startRecordingBtn.addEventListener('click', startRecording);
            elements.stopRecordingBtn.addEventListener('click', stopRecording);
            elements.playRecordingBtn.addEventListener('click', playRecording);
            elements.pauseRecordingBtn.addEventListener('click', pauseRecording);
            elements.downloadRecordingBtn.addEventListener('click', downloadRecording);
            
            // Update preview when settings change
            const previewUpdateElements = [
                elements.aspectRatio, elements.layout,
                elements.questionColor, elements.answerColor,
                elements.textColor, elements.bgColor,
                elements.gradientColor1, elements.gradientColor2,
                elements.countdownEnabled, elements.countdownPosition,
                elements.countdownColor, elements.boxOpacity,
                elements.boxBorder, elements.questionTransition,
                elements.answerTransition, elements.textShadow
            ];
            
            previewUpdateElements.forEach(el => {
                el.addEventListener('input', updatePreview);
                el.addEventListener('change', updatePreview);
            });
        }

        // Background video functions
        function handleVideoUpload(e) {
            const file = e.target.files[0];
            if (file) {
                // Revoke previous URL if exists
                if (state.backgroundVideo) {
                    URL.revokeObjectURL(state.backgroundVideo);
                }
                
                const videoURL = URL.createObjectURL(file);
                elements.bgVideoElement.src = videoURL;
                elements.bgVideoElement.style.display = 'block';
                state.backgroundVideo = videoURL;
                
                // Add event listeners for better error handling
                elements.bgVideoElement.onerror = function() {
                    console.error("Error loading video:", this.error);
                    alert("Error loading video. Please try a different file format (MP4, WebM).");
                };
                
                elements.bgVideoElement.oncanplay = function() {
                    this.play().catch(e => {
                        console.log("Video autoplay prevented:", e);
                        // Mute and try again if autoplay was blocked
                        this.muted = true;
                        this.play().catch(e => console.log("Muted play also prevented:", e));
                    });
                };
            }
        }

        function updateVideoVolume() {
            elements.bgVideoElement.volume = elements.videoVolume.value;
        }

        // Audio functions
        function handleAudioUpload(e) {
            const file = e.target.files[0];
            if (file) {
                // Revoke previous URL if exists
                if (state.backgroundAudio) {
                    URL.revokeObjectURL(state.backgroundAudio);
                }
                
                const audioURL = URL.createObjectURL(file);
                elements.bgAudioElement.src = audioURL;
                state.backgroundAudio = audioURL;
                
                // Add event listeners for better error handling
                elements.bgAudioElement.onerror = function() {
                    console.error("Error loading audio:", this.error);
                    alert("Error loading audio. Please try a different file format (MP3, WAV, OGG).");
                };
                
                // Don't autoplay audio immediately due to browser restrictions
                // It will be played during recording when user interaction has occurred
            }
        }

        function updateAudioVolume() {
            elements.bgAudioElement.volume = elements.audioVolume.value;
        }

        function updateAudioLoop() {
            elements.bgAudioElement.loop = elements.audioLoop.checked;
        }

        // Branding functions
        function updateBrandingControls() {
            const type = elements.brandingType.value;
            elements.brandingLogoControl.style.display = type === 'logo' ? 'block' : 'none';
            elements.brandingTextControl.style.display = type === 'text' ? 'block' : 'none';
            
            if (type === 'none') {
                elements.brandingElement.style.display = 'none';
            } else {
                elements.brandingElement.style.display = 'block';
                updateBranding();
            }
        }

        function handleLogoUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    state.brandingLogo = event.target.result;
                    updateBranding();
                };
                reader.readAsDataURL(file);
            }
        }

        function updateBranding() {
            const type = elements.brandingType.value;
            elements.brandingElement.innerHTML = '';
            
            if (type === 'logo' && state.brandingLogo) {
                const img = document.createElement('img');
                img.src = state.brandingLogo;
                img.className = 'branding-logo';
                elements.brandingElement.appendChild(img);
                updateBrandingPosition();
                updateBrandingSize();
            } else if (type === 'text' && elements.brandingTextInput.value) {
                const text = document.createElement('div');
                text.className = 'branding-text';
                text.textContent = elements.brandingTextInput.value;
                elements.brandingElement.appendChild(text);
                updateBrandingPosition();
                updateBrandingText();
                updateBrandingSize();
            }
        }

        function updateBrandingPosition() {
            const type = elements.brandingType.value;
            const position = type === 'logo' ? elements.logoPosition.value : elements.textPosition.value;
            
            elements.brandingElement.style.top = position.includes('top') ? '10px' : 'auto';
            elements.brandingElement.style.bottom = position.includes('bottom') ? '10px' : 'auto';
            elements.brandingElement.style.left = position.includes('left') ? '10px' : 'auto';
            elements.brandingElement.style.right = position.includes('right') ? '10px' : 'auto';
        }

        function updateBrandingText() {
            const textElement = elements.brandingElement.querySelector('.branding-text');
            if (textElement) {
                textElement.style.fontFamily = elements.textFont.value;
                textElement.style.color = elements.brandingTextColor.value;
            }
        }

        function updateBrandingSize() {
            const type = elements.brandingType.value;
            if (type === 'logo') {
                const img = elements.brandingElement.querySelector('.branding-logo');
                if (img) {
                    img.style.maxWidth = `${elements.logoSize.value}%`;
                }
            } else if (type === 'text') {
                const text = elements.brandingElement.querySelector('.branding-text');
                if (text) {
                    text.style.fontSize = `${elements.textSize.value}px`;
                }
            }
        }

        // Font functions
        function updateFontSizeDisplay() {
            elements.fontSizeValue.textContent = `${state.currentFontSize}rem`;
            elements.questionBox.style.fontSize = `${state.currentFontSize}rem`;
            elements.answerBox.style.fontSize = `${state.currentFontSize}rem`;
        }

        function decreaseFontSize() {
            if (state.currentFontSize > 0.8) {
                state.currentFontSize -= 0.1;
                updateFontSizeDisplay();
            }
        }

        function increaseFontSize() {
            if (state.currentFontSize < 2.5) {
                state.currentFontSize += 0.1;
                updateFontSizeDisplay();
            }
        }

        function updateFontFamily() {
            const fontFamily = elements.fontFamily.value;
            elements.questionBox.style.fontFamily = fontFamily;
            elements.answerBox.style.fontFamily = fontFamily;
        }

        // Line height functions
        function updateLineHeightDisplay() {
            elements.qLineValue.textContent = state.questionLineHeight;
            elements.aLineValue.textContent = state.answerLineHeight;
            elements.questionBox.style.lineHeight = state.questionLineHeight;
            elements.answerBox.style.lineHeight = state.answerLineHeight;
        }

        function changeLineHeight(type, change) {
            if (type === 'question') {
                state.questionLineHeight = Math.max(1, Math.min(3, state.questionLineHeight + change));
            } else {
                state.answerLineHeight = Math.max(1, Math.min(3, state.answerLineHeight + change));
            }
            updateLineHeightDisplay();
        }

        // Quiz functionality functions
        function updateBackgroundControls() {
            const type = elements.bgType.value;
            elements.bgColorControl.style.display = type === 'color' ? 'block' : 'none';
            elements.bgImageControl.style.display = type === 'image' ? 'block' : 'none';
            elements.bgVideoControl.style.display = type === 'video' ? 'block' : 'none';
            elements.bgGradientControl.style.display = type === 'gradient' ? 'block' : 'none';
            
            if (type === 'video') {
                elements.bgVideoElement.style.display = 'block';
            } else {
                elements.bgVideoElement.style.display = 'none';
            }
            
            updatePreview();
        }

        function handleImageUpload(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = (event) => {
                    state.backgroundImage = event.target.result;
                    updatePreview();
                };
                reader.readAsDataURL(file);
            }
        }

        function playSound(type) {
            if (!state.audioContext) return;
            
            const oscillator = state.audioContext.createOscillator();
            const gainNode = state.audioContext.createGain();
            
            oscillator.connect(gainNode);
            gainNode.connect(state.audioContext.destination);
            
            if (type === 'question') {
                oscillator.frequency.value = 880;
            } else if (type === 'answer') {
                oscillator.frequency.value = 440;
            } else if (type === 'tick') {
                oscillator.frequency.value = 660;
            }
            
            gainNode.gain.setValueAtTime(0.5, state.audioContext.currentTime);
            oscillator.start();
            gainNode.gain.exponentialRampToValueAtTime(0.01, state.audioContext.currentTime + 0.3);
            oscillator.stop(state.audioContext.currentTime + 0.3);
        }

        function updatePreview() {
            // Aspect ratio
            elements.previewContainer.style.aspectRatio = elements.aspectRatio.value;
            
            // Layout
            if (elements.layout.value === 'stacked') {
                elements.videoFrame.style.flexDirection = 'column';
                elements.videoFrame.style.gap = '20px';
            } else {
                elements.videoFrame.style.flexDirection = 'row';
                elements.videoFrame.style.gap = '40px';
            }
            
            // Colors
            elements.questionBox.style.backgroundColor = elements.questionColor.value;
            elements.answerBox.style.backgroundColor = elements.answerColor.value;
            elements.questionBox.style.color = elements.textColor.value;
            elements.answerBox.style.color = elements.textColor.value;
            
            // Box styles
            elements.questionBox.style.opacity = elements.boxOpacity.value;
            elements.answerBox.style.opacity = elements.boxOpacity.value;
            elements.questionBox.style.border = `${elements.boxBorder.value}px solid rgba(0,0,0,0.2)`;
            elements.answerBox.style.border = `${elements.boxBorder.value}px solid rgba(0,0,0,0.2)`;
            
            // Text shadow
            if (elements.textShadow.checked) {
                elements.questionBox.style.textShadow = '1px 1px 3px rgba(0,0,0,0.5)';
                elements.answerBox.style.textShadow = '1px 1px 3px rgba(0,0,0,0.5)';
            } else {
                elements.questionBox.style.textShadow = 'none';
                elements.answerBox.style.textShadow = 'none';
            }
            
            // Background
            switch(elements.bgType.value) {
                case 'color':
                    elements.previewContainer.style.background = elements.bgColor.value;
                    break;
                case 'image':
                    if (state.backgroundImage) {
                        elements.previewContainer.style.background = `url('${state.backgroundImage}')`;
                        elements.previewContainer.style.backgroundSize = 'cover';
                    }
                    break;
                case 'video':
                    // Video is handled separately
                    break;
                case 'gradient':
                    const direction = elements.gradientDirection.value;
                    if (direction === 'circle') {
                        elements.previewContainer.style.background = 
                            `radial-gradient(${elements.gradientColor1.value}, ${elements.gradientColor2.value})`;
                    } else {
                        elements.previewContainer.style.background = 
                            `linear-gradient(${direction}, ${elements.gradientColor1.value}, ${elements.gradientColor2.value})`;
                    }
                    break;
            }
            
            // Countdown styling
            elements.countdown.style.color = elements.countdownColor.value;
            
            // Position countdown
            positionCountdown();
        }

        function positionCountdown() {
            elements.countdown.style.top = '';
            elements.countdown.style.bottom = '';
            elements.countdown.style.left = '';
            elements.countdown.style.right = '';
            elements.countdown.style.transform = '';
            
            if (elements.countdownPosition.value === 'below') {
                elements.countdown.style.top = 'calc(50% + 60px)';
                elements.countdown.style.left = '50%';
                elements.countdown.style.transform = 'translateX(-50%)';
            } else if (elements.countdownPosition.value === 'top-right') {
                elements.countdown.style.top = '10px';
                elements.countdown.style.right = '10px';
            } else if (elements.countdownPosition.value === 'top-left') {
                elements.countdown.style.top = '10px';
                elements.countdown.style.left = '10px';
            }
        }

        function parseQAInput() {
            const lines = elements.qaInput.value.split('\n');
            state.qaPairs = lines
                .filter(line => line.trim() !== '')
                .map(line => {
                    const [question, answer] = line.split('|').map(part => part.trim());
                    return { question, answer };
                })
                .filter(qa => qa.question && qa.answer);
            
            return state.qaPairs.length > 0;
        }

        function showQuestion(index) {
            if (index < 0 || index >= state.qaPairs.length) return false;
            
            hideQA();
            hideCountdown();
            
            elements.questionBox.textContent = state.qaPairs[index].question;
            
            // Apply transition
            const transition = elements.questionTransition.value;
            elements.questionBox.className = 'question-box';
            setTimeout(() => {
                elements.questionBox.classList.add('visible');
                elements.questionBox.classList.add(`question-${transition}`);
            }, 10);
            
            playSound('question');
            
            return true;
        }

        function showAnswer(index) {
            if (index < 0 || index >= state.qaPairs.length) return false;
            
            elements.answerBox.textContent = state.qaPairs[index].answer;
            
            // Apply transition
            const transition = elements.answerTransition.value;
            elements.answerBox.className = 'answer-box';
            setTimeout(() => {
                elements.answerBox.style.display = 'block';
                elements.answerBox.classList.add('visible');
                elements.answerBox.classList.add(`answer-${transition}`);
            }, 10);
            
            playSound('answer');
            
            return true;
        }

        function hideQA() {
            elements.questionBox.classList.remove('visible');
            elements.answerBox.classList.remove('visible');
            // Reset all animation classes
            elements.questionBox.className = 'question-box';
            elements.answerBox.className = 'answer-box';
            // Hide answer box completely
            elements.answerBox.style.display = 'none';
        }

        function showCountdown(seconds, callback) {
            if (!elements.countdownEnabled.checked) {
                if (callback) setTimeout(callback, seconds * 1000);
                return;
            }
            
            let remaining = seconds;
            elements.countdown.textContent = remaining;
            elements.countdown.classList.add('visible');
            
            state.countdownInterval = setInterval(() => {
                remaining--;
                elements.countdown.textContent = remaining;
                
                playSound('tick');
                
                if (remaining <= 0) {
                    clearInterval(state.countdownInterval);
                    hideCountdown();
                    if (callback) {
                        // Wait half second before showing answer
                        setTimeout(callback, 500);
                    }
                }
            }, 1000);
        }

        function hideCountdown() {
            elements.countdown.classList.remove('visible');
        }

        function queueAction(action) {
            state.actionQueue.push(action);
            if (!state.isProcessingAction) {
                processNextAction();
            }
        }

        function processNextAction() {
            if (state.actionQueue.length === 0) {
                state.isProcessingAction = false;
                return;
            }
            
            state.isProcessingAction = true;
            const action = state.actionQueue.shift();
            action(() => {
                setTimeout(processNextAction, 0);
            });
        }

        function previewFirstQA() {
            if (!parseQAInput()) {
                alert('Please enter valid questions and answers.');
                return;
            }
            
            updatePreview();
            
            queueAction((next) => {
                showQuestion(0);
                setTimeout(() => {
                    showCountdown(elements.countdownTime.value, () => {
                        showAnswer(0);
                        setTimeout(() => {
                            hideQA();
                            next();
                        }, 5000); // Answer remains for 5 seconds
                    });
                    next();
                }, elements.questionTime.value * 1000);
            });
        }

        function startFullPreview() {
            if (!parseQAInput()) {
                alert('Please enter valid questions and answers.');
                return;
            }
            
            state.isPreviewing = true;
            state.currentIndex = 0;
            elements.startPreviewBtn.disabled = true;
            elements.stopPreviewBtn.disabled = false;
            updatePreview();
            
            runPreviewSequence();
        }

        function runPreviewSequence() {
            if (!state.isPreviewing || state.currentIndex >= state.qaPairs.length) {
                stopFullPreview();
                return;
            }
            
            queueAction((next) => {
                showQuestion(state.currentIndex);
                setTimeout(() => {
                    showCountdown(elements.countdownTime.value, () => {
                        // Show answer after countdown ends (with 0.5s delay)
                        showAnswer(state.currentIndex);
                        
                        setTimeout(() => {
                            hideQA();
                            state.currentIndex++;
                            
                            setTimeout(() => {
                                runPreviewSequence();
                                next();
                            }, 1000); // 1 second delay between questions
                        }, 5000); // Answer remains for 5 seconds
                    });
                    next();
                }, elements.questionTime.value * 1000);
            });
        }

        function stopFullPreview() {
            state.isPreviewing = false;
            clearInterval(state.countdownInterval);
            hideCountdown();
            elements.startPreviewBtn.disabled = false;
            elements.stopPreviewBtn.disabled = true;
            hideQA();
            state.actionQueue = [];
            state.isProcessingAction = false;
        }

        // Recording functions
        async function startRecording() {
            if (state.isPreviewing) {
                alert("Please stop the preview before recording.");
                return;
            }
            
            if (!parseQAInput()) {
                alert("Please enter valid questions and answers first.");
                return;
            }
            
            try {
                // Ensure media elements are ready
                if (elements.bgVideoElement.src && !elements.bgVideoElement.muted) {
                    elements.bgVideoElement.muted = true; // Mute for autoplay
                    await elements.bgVideoElement.play().catch(e => {
                        console.log("Video play failed, continuing muted:", e);
                    });
                }
                
                if (elements.bgAudioElement.src) {
                    elements.bgAudioElement.volume = elements.audioVolume.value;
                    await elements.bgAudioElement.play().catch(e => {
                        console.log("Audio play failed, continuing without audio:", e);
                    });
                }
                
                state.recording = true;
                state.currentIndex = 0;
                
                // Hide UI elements during recording
                elements.recordingPreviewContainer.style.display = 'none';
                document.querySelectorAll('.section button:not(.recording-btn)').forEach(btn => {
                    btn.style.visibility = 'hidden';
                });
                
                // Create canvas for capturing
                const canvas = document.createElement('canvas');
                const previewElement = elements.previewContainer;
                canvas.width = previewElement.offsetWidth;
                canvas.height = previewElement.offsetHeight;
                
                // Create stream from canvas
                const stream = canvas.captureStream(25); // 25 FPS
                state.mediaStream = stream;
                
                // Setup RecordRTC
                state.recorder = new RecordRTC(stream, {
                    type: 'video',
                    mimeType: 'video/webm',
                    frameRate: 25,
                    quality: 1,
                    disableLogs: true,
                    timeSlice: 1000, // 1 second chunks
                    ondataavailable: function(blob) {
                        // We'll handle the final blob in stopRecording
                    }
                });
                
                // Start recording
                state.recorder.startRecording();
                state.recordingStartTime = Date.now();
                updateRecordingTimer();
                state.timerInterval = setInterval(updateRecordingTimer, 1000);
                
                // Update UI
                elements.startRecordingBtn.disabled = true;
                elements.stopRecordingBtn.disabled = false;
                elements.recordingStatus.style.display = 'flex';
                
                // Start capturing frames
                state.isCapturingFrames = true;
                captureFrames();
                
                // Begin the quiz sequence
                runRecordingSequence();
                
            } catch (error) {
                console.error("Recording initialization failed:", error);
                stopRecording();
                alert("Recording failed to start. Please try again.");
            }
        }

        async function captureFrames() {
            if (!state.recording || !state.isCapturingFrames) return;
            
            try {
                const canvas = document.createElement('canvas');
                const previewElement = elements.previewContainer;
                canvas.width = previewElement.offsetWidth;
                canvas.height = previewElement.offsetHeight;
                
                // Capture frame with html2canvas
                await html2canvas(previewElement, {
                    scale: 1,
                    logging: false,
                    useCORS: true,
                    allowTaint: true,
                    backgroundColor: null
                }).then(capturedCanvas => {
                    if (state.recorder && state.recording) {
                        // Draw to our canvas
                        const ctx = canvas.getContext('2d');
                        ctx.drawImage(capturedCanvas, 0, 0, canvas.width, canvas.height);
                    }
                });
                
                // Continue capturing
                if (state.isCapturingFrames) {
                    setTimeout(captureFrames, 40); // ~25fps
                }
            } catch (error) {
                console.error("Frame capture error:", error);
                stopRecording();
            }
        }

        function stopRecording() {
            if (!state.recording) return;
            
            state.isCapturingFrames = false;
            state.recording = false;
            clearInterval(state.timerInterval);
            
            // Pause media elements
            elements.bgVideoElement.pause();
            elements.bgAudioElement.pause();
            
            if (state.recorder) {
                state.recorder.stopRecording(() => {
                    state.recordedBlob = state.recorder.getBlob();
                    state.recordingDuration = Math.floor((Date.now() - state.recordingStartTime) / 1000);
                    
                    // Display the recorded video
                    elements.recordingPreviewVideo.src = URL.createObjectURL(state.recordedBlob);
                    elements.recordingPreviewContainer.style.display = 'block';
                    updateRecordingTimeDisplay();
                    
                    // Enable download button
                    elements.downloadRecordingBtn.disabled = false;
                    
                    // Clean up
                    if (state.mediaStream) {
                        state.mediaStream.getTracks().forEach(track => track.stop());
                    }
                    state.recorder.destroy();
                    state.recorder = null;
                    state.mediaStream = null;
                });
            }
            
            // Restore UI
            document.querySelectorAll('.section button').forEach(btn => {
                btn.style.visibility = 'visible';
            });
            elements.startRecordingBtn.disabled = false;
            elements.stopRecordingBtn.disabled = true;
            elements.recordingStatus.style.display = 'none';
        }

        function runRecordingSequence() {
            if (!state.recording || state.currentIndex >= state.qaPairs.length) {
                stopRecording();
                return;
            }
            
            showQuestion(state.currentIndex);
            
            setTimeout(() => {
                showCountdown(elements.countdownTime.value, () => {
                    showAnswer(state.currentIndex);
                    
                    setTimeout(() => {
                        hideQA();
                        state.currentIndex++;
                        
                        setTimeout(() => {
                            runRecordingSequence();
                        }, 1000);
                    }, elements.answerTime.value * 1000);
                });
            }, elements.questionTime.value * 1000);
        }

        function playRecording() {
            elements.recordingPreviewVideo.play();
        }

        function pauseRecording() {
            elements.recordingPreviewVideo.pause();
        }

        function downloadRecording() {
            if (!state.recordedBlob) {
                alert("No recording available to download.");
                return;
            }
            
            const fileName = `quiz-video-${new Date().toISOString().slice(0, 10)}.webm`;
            RecordRTC.invokeSaveAsDialog(state.recordedBlob, fileName);
        }

        function updateRecordingTimer() {
            const elapsed = Math.floor((Date.now() - state.recordingStartTime) / 1000);
            const minutes = Math.floor(elapsed / 60).toString().padStart(2, '0');
            const seconds = (elapsed % 60).toString().padStart(2, '0');
            elements.recordingTimer.textContent = `${minutes}:${seconds}`;
        }

        function updateRecordingTimeDisplay() {
            const minutes = Math.floor(state.recordingDuration / 60).toString().padStart(2, '0');
            const seconds = (state.recordingDuration % 60).toString().padStart(2, '0');
            elements.recordingTime.textContent = `${minutes}:${seconds}`;
        }

        // Initialize
        init();
    </script>
</body>
</html>
