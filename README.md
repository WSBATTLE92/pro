<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Balloon Shooter - 60 Levels Edition</title>
    <style>
        :root {
            --primary: #ff4757;
            --primary-hover: #ff6b81;
            --secondary: #2ed573;
            --accent: #ffa502;
            --dark-bg: rgba(15, 15, 26, 0.95);
            --glass: rgba(255, 255, 255, 0.08);
            --glass-border: rgba(255, 255, 255, 0.15);
            --text-main: #ffffff;
            --text-muted: #a4b0be;
        }

        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-user-select: none;
            margin: 0;
            padding: 0;
            touch-action: none;
        }

        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background: #0d0d1a;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            color: var(--text-main);
        }

        /* Ambient vibrant sky background wrapper */
        #game-wrapper {
            position: relative;
            width: 100%;
            height: 100%;
            max-width: 540px;
            margin: 0 auto;
            overflow: hidden;
            background: linear-gradient(to bottom, #4da4ff 0%, #8ae9ff 70%, #6cd94e 100%);
            box-shadow: 0 0 40px rgba(0, 0, 0, 0.6);
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 2;
        }

        /* Rolling Green Hills Background Elements */
        .hill-back {
            position: absolute;
            bottom: 0;
            left: -10%;
            width: 120%;
            height: 130px;
            background: #52c234;
            border-radius: 50% 50% 0 0;
            z-index: 1;
            opacity: 0.85;
        }
        .hill-front {
            position: absolute;
            bottom: -15px;
            left: -10%;
            width: 130%;
            height: 95px;
            background: #4ac02a;
            border-radius: 45% 45% 0 0;
            z-index: 1;
        }

        /* Clouds Float Setup */
        @keyframes floatCloud {
            0% { transform: translateX(-150px); }
            100% { transform: translateX(560px); }
        }
        .clouds-container {
            position: absolute;
            top: 50px;
            left: 0;
            width: 100%;
            height: 130px;
            overflow: hidden;
            pointer-events: none;
            z-index: 1;
        }
        .cloud {
            position: absolute;
            background: rgba(255, 255, 255, 0.85);
            border-radius: 50px;
            width: 90px;
            height: 32px;
            animation: floatCloud linear infinite;
        }
        .cloud::before, .cloud::after {
            content: '';
            position: absolute;
            background: rgba(255, 255, 255, 0.85);
            border-radius: 50%;
        }
        .cloud::before {
            width: 45px;
            height: 45px;
            top: -22px;
            left: 12px;
        }
        .cloud::after {
            width: 35px;
            height: 35px;
            top: -12px;
            left: 45px;
        }

        /* Screen overlays style sheet */
        .screen {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
            padding: 30px 20px;
            text-align: center;
            z-index: 10;
            transition: opacity 0.3s ease, visibility 0.3s ease;
            opacity: 1;
            visibility: visible;
        }

        .screen.hidden {
            opacity: 0;
            visibility: hidden;
            pointer-events: none;
        }

        /* Cartoonish 3D Glossy Buttons & Pedestals */
        .menu-top-bar {
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 5;
        }

        .coin-badge {
            background: linear-gradient(to bottom, #ffd32c, #ff9f43);
            border: 2px solid #20134a;
            border-radius: 20px;
            padding: 5px 15px;
            font-weight: 800;
            color: #20134a;
            display: flex;
            align-items: center;
            gap: 6px;
            box-shadow: 0 4px 0 rgba(0,0,0,0.15);
            font-size: 0.95rem;
        }

        .btn-gear {
            width: 44px;
            height: 44px;
            background: linear-gradient(to bottom, #5ac8ff, #007aff);
            border: 2.5px solid #fff;
            border-radius: 50%;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3), inset 0 2px 0 rgba(255,255,255,0.4);
            cursor: pointer;
            font-size: 1.4rem;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.1s;
        }
        .btn-gear:active {
            transform: scale(0.9);
        }

        /* Curved Bubble Arch */
        .menu-bubbles {
            display: flex;
            justify-content: space-between;
            width: 100%;
            max-width: 440px;
            margin-top: -10px;
        }
        .menu-bubble {
            width: 42px;
            height: 42px;
            border-radius: 50%;
            background: radial-gradient(circle at 30% 30%, #fff 0%, var(--bubble-color) 65%, rgba(0,0,0,0.3) 100%);
            box-shadow: inset -3px -3px 8px rgba(0,0,0,0.3), 0 4px 6px rgba(0,0,0,0.15);
        }

        /* Cartoon Balloon Typography */
        .bubble-title {
            font-family: 'Arial Black', Impact, sans-serif;
            font-size: 2.5rem;
            font-weight: 900;
            color: #fff;
            -webkit-text-stroke: 2.5px #20134a;
            text-shadow: 0 6px 0 #20134a, 0 10px 15px rgba(0, 0, 0, 0.4);
            line-height: 1.1;
            margin-top: 10px;
            letter-spacing: -1px;
        }
        .bubble-title span {
            color: #fffa65;
        }

        /* Menu Action Columns Grid */
        .menu-grid {
            display: grid;
            grid-template-columns: 1fr 1.3fr 1fr;
            width: 100%;
            align-items: center;
            justify-items: center;
            margin: 15px 0;
            z-index: 5;
        }

        .menu-col {
            display: flex;
            flex-direction: column;
            gap: 16px;
            width: 100%;
        }

        /* Menu Button design matching image */
        .menu-btn {
            position: relative;
            aspect-ratio: 1 / 1;
            border-radius: 18px;
            border: 2.5px solid #fff;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 8px 4px;
            box-shadow: 0 5px 0 rgba(0,0,0,0.35), inset 0 2.5px 0 rgba(255,255,255,0.4), 0 8px 12px rgba(0,0,0,0.2);
            transition: transform 0.1s, box-shadow 0.1s;
        }
        .menu-btn:active {
            transform: translateY(4px);
            box-shadow: 0 1px 0 rgba(0,0,0,0.35), inset 0 1.5px 0 rgba(255,255,255,0.4);
        }

        /* Custom Menu Button Colors */
        .purple-btn {
            background: linear-gradient(to bottom, #d15aff, #a020f0);
        }
        .blue-btn {
            background: linear-gradient(to bottom, #4facfe, #00f2fe);
        }
        .orange-btn {
            background: linear-gradient(to bottom, #ff9f43, #ee5253);
        }
        .lightblue-btn {
            background: linear-gradient(to bottom, #00d2d3, #0abde3);
        }
        .pink-btn {
            background: linear-gradient(to bottom, #ff8da1, #ff2a68);
        }
        .violet-btn {
            background: linear-gradient(to bottom, #9b5de5, #f15bb5);
        }

        .menu-btn-icon {
            font-size: 1.8rem;
            filter: drop-shadow(0 2px 3px rgba(0,0,0,0.25));
            margin-bottom: 4px;
        }

        .menu-btn-text {
            font-family: 'Arial Black', Impact, sans-serif;
            font-size: 0.65rem;
            font-weight: 900;
            color: #fff;
            -webkit-text-stroke: 1px #20134a;
            text-shadow: 0 1.5px 0 #20134a;
            text-transform: uppercase;
        }

        /* Pulsing Alert Badge */
        .btn-badge {
            position: absolute;
            top: -6px;
            right: -6px;
            background: #ff4757;
            color: white;
            font-weight: 900;
            font-size: 0.75rem;
            width: 18px;
            height: 18px;
            border-radius: 50%;
            border: 1.5px solid white;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 2px 4px rgba(0,0,0,0.3);
            animation: pulseBadge 0.6s infinite alternate;
        }
        @keyframes pulseBadge {
            from { transform: scale(1); }
            to { transform: scale(1.15); }
        }

        /* Center Play Pedestal Style */
        .menu-center {
            position: relative;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }

        .pedestal {
            width: 90px;
            height: 24px;
            background: linear-gradient(to bottom, #bdc3c7, #7f8c8d);
            border: 2px solid #20134a;
            border-radius: 50%;
            position: absolute;
            bottom: -5px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.3);
        }

        /* Big Green Play Button */
        .play-circle-btn {
            width: 96px;
            height: 96px;
            border-radius: 50%;
            border: 4px solid #ffa502;
            background: radial-gradient(circle at 35% 35%, #9fff3b 0%, #1fab09 65%, #0c6a02 100%);
            box-shadow: 0 6px 0 #d48000, 0 8px 15px rgba(0,0,0,0.35), inset 0 3px 0 rgba(255,255,255,0.4);
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 2;
            margin-bottom: 8px;
            transition: transform 0.1s;
            animation: pulsePlay 1.5s infinite alternate;
        }
        .play-circle-btn:active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 #d48000, 0 3px 5px rgba(0,0,0,0.25);
        }

        @keyframes pulsePlay {
            from { transform: scale(1); }
            to { transform: scale(1.05); }
        }

        .play-arrow {
            font-size: 1.9rem;
            color: white;
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
            margin-left: 6px;
            line-height: 1;
        }

        .play-text {
            font-family: 'Arial Black', Impact, sans-serif;
            font-size: 0.95rem;
            font-weight: 900;
            color: #fff;
            -webkit-text-stroke: 1.5px #20134a;
            text-shadow: 0 2px 0 #20134a;
        }

        /* Capsule Bottom Row Buttons */
        .menu-bottom-row {
            display: flex;
            gap: 15px;
            width: 100%;
            justify-content: center;
            z-index: 5;
            margin-bottom: 10px;
        }

        .menu-capsule-btn {
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 8px 18px;
            border-radius: 25px;
            border: 2.5px solid #fff;
            box-shadow: 0 4px 0 rgba(0,0,0,0.25), inset 0 2px 0 rgba(255,255,255,0.4);
            cursor: pointer;
            transition: transform 0.1s;
        }
        .menu-capsule-btn:active {
            transform: translateY(3px);
            box-shadow: 0 1px 0 rgba(0,0,0,0.25);
        }

        .capsule-icon {
            font-size: 1.1rem;
        }

        .capsule-text {
            font-family: 'Arial Black', Impact, sans-serif;
            font-size: 0.75rem;
            font-weight: 900;
            color: #fff;
            -webkit-text-stroke: 1px #20134a;
            text-shadow: 0 1.5px 0 #20134a;
        }

        /* Bottom Center Launcher Graphic Decor */
        .launcher-container {
            width: 80px;
            height: 48px;
            background: linear-gradient(to top, #30336b, #130cb7);
            border-radius: 40px 40px 10px 10px;
            border: 2.5px solid #ffa502;
            z-index: 4;
            display: flex;
            justify-content: center;
            align-items: center;
            box-shadow: 0 -3px 10px rgba(0,0,0,0.25);
            margin-bottom: -15px;
        }
        .launcher-orb {
            width: 34px;
            height: 34px;
            border-radius: 50%;
            background: radial-gradient(circle at 30% 30%, #fff 0%, #0984e3 65%, #000 100%);
            box-shadow: inset -3px -3px 8px rgba(0,0,0,0.5), 0 3px 8px rgba(0,0,0,0.3);
        }

        /* Standardized Glass Screens styling (Pause / GameOver) */
        .screen-dark {
            background: rgba(10, 10, 20, 0.9);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
        }

        h1.neon-title {
            font-size: 2.8rem;
            font-weight: 900;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #ff4757, #ff7f50, #eccc68);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
        }

        /* High-fidelity Glassmorphic Buttons */
        .btn {
            background: linear-gradient(135deg, var(--primary), #ff6b81);
            color: #fff;
            border: none;
            padding: 15px 32px;
            font-size: 1.1rem;
            font-weight: 700;
            border-radius: 16px;
            cursor: pointer;
            width: 100%;
            max-width: 260px;
            margin: 10px 0;
            box-shadow: 0 8px 20px rgba(255, 71, 87, 0.3);
            transition: transform 0.15s ease, box-shadow 0.15s ease;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        .btn:active {
            transform: scale(0.97);
        }
        .btn-alt {
            background: var(--glass);
            border: 1.5px solid var(--glass-border);
            color: var(--text-main);
            box-shadow: none;
        }
        .btn-green {
            background: linear-gradient(135deg, #2ed573, #7bed9f);
            box-shadow: 0 8px 20px rgba(46, 213, 115, 0.3);
        }

        /* Head-Up Display (HUD) */
        #hud {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            padding: env(safe-area-inset-top, 16px) 20px 10px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 5;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.3s;
        }
        #hud.visible {
            pointer-events: auto;
            opacity: 1;
        }

        .hud-group {
            background: rgba(0,0,0,0.5);
            border: 1px solid var(--glass-border);
            padding: 8px 16px;
            border-radius: 12px;
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 700;
            font-size: 0.95rem;
        }

        .score-num {
            color: var(--accent);
            font-size: 1.1rem;
        }

        .hearts {
            display: flex;
            gap: 4px;
            color: var(--primary);
            font-size: 1.1rem;
        }

        .heart-icon {
            transition: transform 0.2s ease, opacity 0.2s ease;
        }

        .heart-icon.lost {
            opacity: 0.2;
            transform: scale(0.8);
        }

        .btn-pause {
            width: 38px;
            height: 38px;
            border-radius: 10px;
            background: rgba(0,0,0,0.5);
            border: 1px solid var(--glass-border);
            color: white;
            font-weight: bold;
            font-size: 0.9rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
        }

        .sub-hud {
            position: absolute;
            top: 75px;
            width: 100%;
            padding: 0 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            pointer-events: none;
            z-index: 5;
        }

        .combo-box {
            background: linear-gradient(135deg, #ffa502, #ff7f50);
            color: #101010;
            font-weight: 800;
            font-size: 0.8rem;
            padding: 4px 10px;
            border-radius: 20px;
            text-transform: uppercase;
            box-shadow: 0 4px 10px rgba(255, 165, 2, 0.3);
            display: none;
            animation: pulse 0.5s infinite alternate;
        }

        .timer-box {
            background: rgba(0, 0, 0, 0.5);
            border: 1.5px solid #2ed573;
            color: #2ed573;
            font-weight: 700;
            font-size: 0.85rem;
            padding: 4px 12px;
            border-radius: 20px;
            margin-left: auto;
            display: none;
        }

        /* Leaderboard table style */
        .score-table {
            width: 100%;
            max-width: 280px;
            margin: 15px 0;
            border-collapse: collapse;
            font-size: 0.95rem;
        }

        .score-table td {
            padding: 8px 12px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.08);
        }

        .score-table tr:last-child td {
            border-bottom: none;
        }

        .score-rank {
            font-weight: bold;
            color: var(--accent);
            width: 40px;
        }

        .score-value {
            text-align: right;
            font-weight: 600;
        }

        /* High-fidelity Cartoon Styled Modals */
        .cartoon-modal {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.65);
            backdrop-filter: blur(6px);
            -webkit-backdrop-filter: blur(6px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.25s, visibility 0.25s;
        }

        .cartoon-modal.active {
            opacity: 1;
            visibility: visible;
        }

        .modal-content {
            width: 88%;
            max-width: 400px;
            background: linear-gradient(135deg, #fffcf0 0%, #ffeaa7 100%);
            border: 5px solid #ff9f43;
            border-radius: 28px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.5), inset 0 4px 0 rgba(255,255,255,0.6);
            position: relative;
            padding: 24px;
            text-align: center;
            color: #20134a;
            animation: modalPop 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
 
