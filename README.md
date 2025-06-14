# MySchool
<!DOCTYPE html>
<html lang="zh-CN" id="html">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>阳江市博雅实验学校留言板.Tim</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', sans-serif;
            transition: all 0.3s ease;
        }

        body {
            background: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background-image: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
        }

        .header {
            position: fixed;
            top: 0;
            width: 100%;
            background: #fff;
            padding: 15px 20px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .header h1 {
            color: #1a73e8;
            font-size: 1.8rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .header h1 span {
            color: #ff4b4b;
        }

        .mode-switch {
            position: absolute;
            right: 20px;
            background: #1a73e8;
            color: white;
            padding: 6px 12px;
            border-radius: 20px;
            cursor: pointer;
        }

        .container {
            width: 100%;
            max-width: 1200px;
            margin-top: 80px;
            padding: 20px;
            display: grid;
            grid-template-columns: 3fr 1fr;
            gap: 20px;
        }

        .留言板 {
            background: #fff;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }

        .留言输入区 {
            margin-bottom: 20px;
        }

        .留言输入区 textarea {
            width: 100%;
            padding: 12px;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            font-size: 1rem;
            resize: vertical;
            min-height: 100px;
        }

        .功能栏 {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 12px;
        }

        .图片上传按钮 {
            background: #1a73e8;
            color: white;
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 4px;
        }

        .图片上传按钮 input {
            display: none;
        }

        .提交按钮 {
            background: #4caf50;
            color: white;
            padding: 8px 24px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }

        .留言列表 {
            margin-top: 24px;
        }

        .留言 {
            background: #f8f9fa;
            border-radius: 8px;
            padding: 16px;
            margin-bottom: 16px;
            display: flex;
            gap: 12px;
            flex-direction: column;
        }

        .留言图片 {
            max-width: 300px;
            max-height: 200px;
            border-radius: 6px;
            object-fit: cover;
            margin-bottom: 12px;
        }

        .侧边栏 {
            background: #fff;
            border-radius: 12px;
            padding: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }

        /* 夜间模式样式 */
        .dark-mode {
            background: #1a1a1a;
        }

        .dark-mode .header,
        .dark-mode .留言板,
        .dark-mode .侧边栏 {
            background: #282828;
            color: #fff;
        }

        .dark-mode .留言 {
            background: #3a3a3a;
        }

        .dark-mode .功能栏 button {
            background: #646cff;
        }

        .timestamp {
            font-size: 0.875rem;
            color: #666;
            margin-top: 8px;
            display: block;
        }
    </style>
</head>
<body>
    <header class="header">
        <h1>阳江市博雅实验学校<span>留言板.Tim</span></h1>
        <div class="mode-switch" onclick="toggleDarkMode()">🌙 夜间模式</div>
    </header>

    <main class="container">
        <section class="留言板">
            <div class="留言输入区">
                <textarea placeholder="请输入你的留言（支持匿名）" required 
                          onkeydown="if(event.keyCode===13&&!event.shiftKey){event.preventDefault();postMessage();}"></textarea>
            </div>
            <div class="功能栏">
                <div style="display: flex; align-items: center; gap: 4px;">
                    <input type="checkbox" id="anonymous" checked>
                    <label for="anonymous">匿名发表</label>
                </div>
                <label class="图片上传按钮" for="image-upload">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 18v-1.5m0 1.5h16.5m0 1.5v-1.5M3.75 9v1.5m0-1.5h16.5m0 1.5v-1.5M3.75 3v1.5m0-1.5h16.5" />
                    </svg>
                    上传图片
                    <input type="file" id="image-upload" accept="image/*">
                </label>
                <button class="提交按钮" onclick="postMessage()" id="submitBtn">提交留言</button>
            </div>

            <div class="留言列表" id="message-list"></div>
        </section>

        <aside class="侧边栏">
            <h3>留言须知</h3>
            <p>1. 本留言板支持匿名发言</p>
            <p>2. 请遵守网络文明规范</p>
            <p>3. 图片大小限制：2MB以内</p>
        </aside>
    </main>

    <script>
        const messageList = document.getElementById('message-list');
        const imageInput = document.getElementById('image-upload');
        let currentImage = null;
        const submitBtn = document.getElementById('submitBtn');

        // 夜间模式切换
        function toggleDarkMode() {
            document.body.classList.toggle('dark-mode');
        }

        // 图片上传处理
        imageInput.addEventListener('change', (e) => {
            const file = e.target.files[0];
            if (file && file.size < 2 * 1024 * 1024) { // 2MB限制
                const reader = new FileReader();
                reader.onload = (event) => {
                    currentImage = event.target.result;
                };
                reader.readAsDataURL(file);
            } else {
                alert('图片大小不能超过2MB！');
                imageInput.value = '';
                currentImage = null;
            }
        });

        // 提交留言
        function postMessage() {
            submitBtn.disabled = true; // 禁用按钮防止重复提交
            const textArea = document.querySelector('textarea');
            const text = textArea.value.trim();
            const isAnonymous = document.getElementById('anonymous').checked;
            const username = isAnonymous ? '匿名用户' : 'Tim'; // 可改为用户输入
            const formattedText = isAnonymous ? text : `${username}：${text}`;
            
            if (!text && !currentImage) {
                alert('请输入留言内容或上传图片');
                submitBtn.disabled = false;
                return;
            }

            const messageDiv = document.createElement('div');
            messageDiv.className = '留言';
            
            // 创建留言内容容器
            const contentDiv = document.createElement('div');
            if (text) {
                contentDiv.innerHTML = `<p>${formattedText}</p>`;
            }

            // 添加时间戳
            const timestamp = new Date().toLocaleString('zh-CN', { 
                hour12: false, 
                year: 'numeric', 
                month: '2-digit', 
                day: '2-digit', 
                hour: '2-digit', 
                minute: '2-digit' 
            });
            contentDiv.innerHTML += `<span class="timestamp">${timestamp}</span>`;

            // 添加图片
            if (currentImage) {
                const img = document.createElement('img');
                img.className = '留言图片';
                img.src = currentImage;
                messageDiv.appendChild(img);
            }

            messageDiv.appendChild(contentDiv);
            messageList.appendChild(messageDiv);
            
            // 清理操作
            textArea.value = '';
            currentImage = null;
            imageInput.value = '';
            submitBtn.disabled = false;
            
            // 限制最多显示20条留言
            if (messageList.children.length > 20) {
                messageList.firstChild.remove();
            }
        }

        // 键盘提交支持（Enter键）
        document.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && e.target === textArea && !e.shiftKey) {
                postMessage();
            }
        });
    </script>
</body>
</html>