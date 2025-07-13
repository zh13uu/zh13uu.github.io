<script>
  // Danh sách các bước: mỗi bước là { type: 'cmd' | 'result', text: string }
  export let steps = [
    { type: 'cmd', text: 'id' },
    { type: 'result', text: 'root at here :)' },
    { type: 'cmd', text: 'ls' },
    { type: 'result', text: 'welcome' },
    { type: 'cmd', text: './welcome' },
    { type: 'result', text: 'Enjoy with me' },
  ];

  let displayedLines = [];
  let typing = '';
  let showCursor = true;
  let currentTime = new Date().toLocaleString();
  let isTyping = false;
  let stepIndex = 0;

  // Thêm biến cho từng ký tự typing
  $: typingChars = typing.split("");

  async function typeTerminal() {
    // Delay khởi tạo cho smooth hơn
    await new Promise(r => setTimeout(r, 1500));
    
    for (let i = 0; i < steps.length; i++) {
      if (steps[i].type === 'cmd') {
        typing = '';
        isTyping = true;
        showCursor = true;
        
        // Delay êm hơn trước khi bắt đầu gõ
        await new Promise(r => setTimeout(r, 600));
        
        // Gõ từng ký tự với hiệu ứng từ trái sang phải - mượt hơn
        for (let j = 0; j < steps[i].text.length; j++) {
          typing = steps[i].text.substring(0, j + 1);
          
          // Tốc độ gõ mượt hơn và tự nhiên hơn (80-200ms)
          let delay = 80 + Math.random() * 120;
          
          // Thêm hiệu ứng ngừng dài hơn tại space và dấu câu
          if (steps[i].text[j] === ' ') {
            delay += 150; // Dừng lâu hơn ở space
            await new Promise(r => setTimeout(r, delay));
            continue;
          } else if (['.', '/', '-', '_'].includes(steps[i].text[j])) {
            delay += 100; // Dừng lâu hơn ở dấu câu
            await new Promise(r => setTimeout(r, delay));
            continue;
          }
          
          await new Promise(r => setTimeout(r, delay));
        }
        
        // Dừng lại lâu hơn sau khi gõ xong để tạo cảm giác tự nhiên
        await new Promise(r => setTimeout(r, 600));
        
        // Gõ xong thì tắt cursor và isTyping trước khi push vào displayedLines
        isTyping = false;
        showCursor = false;
        
        // Push dòng lệnh vào displayedLines
        displayedLines = [...displayedLines, { type: 'cmd', text: typing }];
        typing = '';
        
        // Delay nhẹ nhàng trước khi hiện kết quả
        await new Promise(r => setTimeout(r, 300));
        
      } else {
        // Kết quả hiện ra với hiệu ứng slide in mượt mà
        displayedLines = [...displayedLines, { type: 'result', text: steps[i].text }];
        
        // Delay êm hơn trước lệnh tiếp theo
        await new Promise(r => setTimeout(r, 1000 + Math.random() * 400));
      }
      stepIndex = i + 1;
    }
    
    // Sau khi hoàn thành, restart animation
    // await new Promise(r => setTimeout(r, 3000));
    // restartAnimation();
  }

  function updateTime() {
    currentTime = new Date().toLocaleString();
  }

  // Bắt đầu animation
  typeTerminal();
  setInterval(updateTime, 1000);
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;600&display=swap');
  
  .terminal-container {
    max-width: 800px;
    width: 100%;
    margin: 0 auto;
    position: relative;
  }

  .terminal-window {
    background: 
      linear-gradient(135deg, rgba(13, 17, 23, 0.95) 0%, rgba(1, 4, 9, 0.98) 100%);
    border: 1px solid #30363d;
    border-radius: 12px;
    box-shadow: 
      0 16px 32px rgba(0, 0, 0, 0.6),
      0 0 0 1px rgba(255, 255, 255, 0.05),
      inset 0 1px 0 rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    overflow: hidden;
    position: relative;
  }

  .glow-effect {
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background: radial-gradient(circle, rgba(0, 255, 100, 0.1) 0%, transparent 70%);
    animation: rotate 20s linear infinite;
    pointer-events: none;
  }

  @keyframes rotate {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

  .terminal-header {
    background: linear-gradient(135deg, #21262d 0%, #161b22 100%);
    padding: 12px 20px;
    display: flex;
    align-items: center;
    gap: 8px;
    border-bottom: 1px solid #30363d;
    position: relative;
  }

  .terminal-buttons {
    display: flex;
    gap: 8px;
    align-items: center;
  }

  .terminal-dot {
    width: 13px;
    height: 13px;
    border-radius: 50%;
    position: relative;
    transition: all 0.3s ease;
  }

  .terminal-dot::before {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 100%;
    height: 100%;
    border-radius: 50%;
    transform: translate(-50%, -50%);
    opacity: 0;
    transition: all 0.3s ease;
  }

  .terminal-dot:hover::before {
    opacity: 0.3;
    transform: translate(-50%, -50%) scale(1.5);
  }

  .terminal-dot.red { 
    background: linear-gradient(135deg, #ff5f56 0%, #e0443e 100%);
    box-shadow: 0 2px 4px rgba(255, 95, 86, 0.3);
  }
  .terminal-dot.red::before { background: #ff5f56; }

  .terminal-dot.yellow { 
    background: linear-gradient(135deg, #ffbd2e 0%, #dea123 100%);
    box-shadow: 0 2px 4px rgba(255, 189, 46, 0.3);
  }
  .terminal-dot.yellow::before { background: #ffbd2e; }

  .terminal-dot.green { 
    background: linear-gradient(135deg, #27c93f 0%, #13a10e 100%);
    box-shadow: 0 2px 4px rgba(39, 201, 63, 0.3);
  }
  .terminal-dot.green::before { background: #27c93f; }

  .terminal-title {
    color: #8b949e;
    font-size: 13px;
    font-weight: 500;
    margin-left: 12px;
    opacity: 0.8;
  }

  .terminal-content {
    padding: 24px;
    min-height: 280px;
    background: 
      radial-gradient(circle at 10% 20%, rgba(0, 255, 100, 0.02) 0%, transparent 50%),
      radial-gradient(circle at 90% 80%, rgba(0, 255, 100, 0.02) 0%, transparent 50%);
    position: relative;
    overflow: hidden;
    font-family: 'JetBrains Mono', 'Fira Mono', monospace;
  }

  .terminal-content::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: 
      repeating-linear-gradient(
        0deg,
        transparent,
        transparent 2px,
        rgba(0, 255, 100, 0.02) 2px,
        rgba(0, 255, 100, 0.02) 4px
      );
    pointer-events: none;
    animation: scanlines 0.1s linear infinite;
  }

  @keyframes scanlines {
    0% { transform: translateY(0px); }
    100% { transform: translateY(4px); }
  }

  .terminal-output {
    position: relative;
    z-index: 1;
  }

  .terminal-line {
    color: #f8fafc;
    font-size: 15px;
    line-height: 1.8;
    margin-bottom: 4px;
    font-weight: 500;
    font-family: 'JetBrains Mono', 'Fira Mono', monospace;
    display: flex;
    align-items: baseline;
    min-height: 27px; /* Cố định chiều cao để tránh nhấp nháy */
  }
  
  .terminal-line.fade-in {
    opacity: 0;
    animation: softAppear 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards;
  }
  
  @keyframes softAppear {
    0% {
      opacity: 0;
      transform: translateY(8px);
    }
    100% {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .terminal-prompt {
    color: #ff6b6b;
    font-weight: 600;
    text-shadow: 0 0 6px rgba(255, 107, 107, 0.4);
    font-family: inherit;
    white-space: nowrap;
    flex-shrink: 0; /* Không cho phép co lại */
  }

  .terminal-cmd {
    color: #ffffff;
    font-weight: 500;
    font-family: inherit;
    text-shadow: 0 0 2px rgba(255, 255, 255, 0.3);
    margin-left: 4px;
    display: inline-block;
    min-width: 1px;
    white-space: nowrap;
    overflow: hidden;
  }

  /* Màu xanh lá cho command thực thi */
  .terminal-cmd.executable {
    color: #00ff64;
    text-shadow: 0 0 4px rgba(0, 255, 100, 0.4);
    font-weight: 500;
  }

  .terminal-result {
    color: #f8fafc;
    font-weight: 400;
    text-shadow: 0 0 2px rgba(0, 255, 100, 0.2);
    font-family: inherit;
    opacity: 0.9;
    flex: 1;
  }

  /* Màu xanh lá cho file thực thi */
  .terminal-result.executable {
    color: #00ff64;
    text-shadow: 0 0 4px rgba(0, 255, 100, 0.4);
    font-weight: 500;
  }

  /* Con trỏ nằm ngang */
  .cursor {
    display: inline-block;
    width: 12px;
    height: 3px;
    background: linear-gradient(90deg, #00ff64 0%, #00cc50 100%);
    animation: horizontalBlink 1.5s ease-in-out infinite;
    box-shadow: 
      0 0 6px rgba(0, 255, 100, 0.6),
      0 0 12px rgba(0, 255, 100, 0.3);
    margin-left: 2px;
    margin-bottom: 2px;
    flex-shrink: 0;
    vertical-align: baseline;
    border-radius: 2px;
    position: relative;
    transform: translateY(6px);
  }

  .cursor::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, #ffffff 0%, #00ff64 100%);
    border-radius: 2px;
    opacity: 0.8;
    animation: horizontalCursorGlow 2.5s ease-in-out infinite;
  }

  @keyframes horizontalBlink {
    0%, 45% { 
      opacity: 1; 
      transform: translateY(6px) scaleX(1) scaleY(1);
    }
    50%, 95% { 
      opacity: 0.3; 
      transform: translateY(6px) scaleX(0.8) scaleY(0.9);
    }
    100% { 
      opacity: 1; 
      transform: translateY(6px) scaleX(1) scaleY(1);
    }
  }

  @keyframes horizontalCursorGlow {
    0%, 100% { 
      opacity: 0.5;
      transform: scaleX(1);
    }
    50% { 
      opacity: 0.9;
      transform: scaleX(1.2);
    }
  }

  .status-bar {
    background: linear-gradient(135deg, #161b22 0%, #0d1117 100%);
    padding: 8px 20px;
    border-top: 1px solid #30363d;
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 12px;
    color: #7c8b95;
    font-family: 'JetBrains Mono', 'Fira Mono', monospace;
  }

  .status-item {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .status-indicator {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #00ff64;
    animation: pulse 2s infinite;
  }

  @keyframes pulse {
    0%, 100% { 
      opacity: 1; 
      transform: scale(1);
      box-shadow: 0 0 0 0 rgba(0, 255, 100, 0.7);
    }
    50% { 
      opacity: 0.7; 
      transform: scale(0.9);
      box-shadow: 0 0 0 4px rgba(0, 255, 100, 0);
    }
  }

  .terminal-output {
    scroll-behavior: smooth;
  }

  /* Container cho command line để tránh layout shift */
  .cmd-container {
    display: flex;
    align-items: baseline;
    width: 100%;
    min-height: 27px;
  }

  /* Tối ưu hóa typing effect */
  .typing-container {
    display: flex;
    align-items: baseline;
    flex: 1;
  }

  /* Hiệu ứng typing từ trái sang phải - mượt hơn */
  .typing-text {
    display: inline-block;
    overflow: hidden;
    white-space: nowrap;
    border-right: none;
    animation: typeReveal 0.15s ease-out;
  }

  @keyframes typeReveal {
    0% {
      transform: translateX(-1px);
      opacity: 0.8;
    }
    100% {
      transform: translateX(0);
      opacity: 1;
    }
  }

  /* Loại bỏ các hiệu ứng không cần thiết */
  .terminal-cmd {
    overflow: visible;
    white-space: nowrap;
  }

  .typing-char {
    display: inline-block;
    opacity: 0;
    transform: translateX(-2px) scale(0.995);
    animation: slideInChar 0.18s cubic-bezier(.25,.46,.45,.94) forwards;
  }
  
  @keyframes slideInChar {
    0% {
      opacity: 0;
      transform: translateX(-2px) scale(0.995);
    }
    100% {
      opacity: 1;
      transform: translateX(0) scale(1);
    }
  }
</style>

<div class="terminal-container">
  <div class="terminal-window">
    <div class="glow-effect"></div>
    <div class="terminal-header">
      <div class="terminal-buttons">
        <div class="terminal-dot red"></div>
        <div class="terminal-dot yellow"></div>
        <div class="terminal-dot green"></div>
      </div>
      <div class="terminal-title">nd0@darknet: ~</div>
    </div>
    <div class="terminal-content">
      <div class="terminal-output">
        {#each displayedLines as line, i (i)}
          {#if line.type === 'cmd'}
            <div class="terminal-line">
              <span class="terminal-prompt">nd0@darknet:/# </span>
              <span class="terminal-cmd" class:executable={line.text === './welcome'}>{line.text}</span>
            </div>
          {:else}
            <div class="terminal-line fade-in">
              <span class="terminal-result" class:executable={line.text === 'welcome'}>{line.text}</span>
            </div>
          {/if}
        {/each}
        {#if isTyping}
          <div class="terminal-line">
            <div class="cmd-container">
              <span class="terminal-prompt">nd0@darknet:/# </span>
              <div class="typing-container">
                <span class="terminal-cmd typing-text" class:executable={typing === './welcome'}>
                  {#each typingChars as char, idx}
                    <span class="typing-char" style="animation-delay: {idx * 0.02}s">{char}</span>
                  {/each}
                </span>
                {#if showCursor}
                  <span class="cursor"></span>
                {/if}
              </div>
            </div>
          </div>
        {/if}
      </div>
    </div>
    <div class="status-bar">
      <div class="status-item">
        <span class="status-indicator"></span>
        <span>Online</span>
      </div>
      <div class="status-item">
        <span>Last login: {currentTime}</span>
      </div>
      <div class="status-item">
        <span>Shell: /bin/bash</span>
      </div>
    </div>
  </div>
</div>