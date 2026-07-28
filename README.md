Gemini との会話
情報Ⅰの画像のデジタル表現で、標本化・量子化を体験できるWebアプリを作って下さい。

高校の「情報Ⅰ」の授業や自習で大活躍する、標本化（サンプリング）と量子化（クオンタイズ）の仕組みを直感的に理解できるWebアプリを作成しました！

以下のコードを丸ごとコピーして、メモ帳などに貼り付け、ファイル名を index.html として保存してください。ダブルクリックしてブラウザで開くだけで、インストール不要で今すぐ動かせます。

標本化・量子化シミュレーター（HTMLコード）
HTML
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>情報Ⅰ：画像のデジタル表現シミュレーター</title>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --text: #1e293b;
            --text-light: #64748b;
            --border: #e2e8f0;
        }

        body {
            font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', Meiryo, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            line-height: 1.6;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
        }

        h1 {
            color: var(--primary);
            margin-bottom: 10px;
        }

        p.subtitle {
            color: var(--text-light);
            font-size: 1.1rem;
        }

        .main-layout {
            display: grid;
            grid-template-columns: 1fr;
            gap: 24px;
        }

        @media (min-width: 768px) {
            .main-layout {
                grid-template-columns: 350px 1fr;
            }
        }

        .panel {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 24px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            height: fit-content;
        }

        .panel h2 {
            font-size: 1.25rem;
            margin-top: 0;
            margin-bottom: 20px;
            border-bottom: 2px solid var(--border);
            padding-bottom: 10px;
        }

        .control-group {
            margin-bottom: 20px;
        }

        .control-group label {
            display: block;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .control-desc {
            font-size: 0.85rem;
            color: var(--text-light);
            margin-bottom: 8px;
            margin-top: -4px;
        }

        input[type="range"] {
            width: 100%;
            margin-bottom: 8px;
            accent-color: var(--primary);
        }

        .value-display {
            display: flex;
            justify-content: space-between;
            font-size: 0.9rem;
            font-weight: bold;
            color: var(--primary);
        }

        .file-input-wrapper {
            margin-top: 15px;
        }

        .file-input-btn {
            display: inline-block;
            width: 100%;
            padding: 10px;
            background-color: #f1f5f9;
            border: 2px dashed #cbd5e1;
            border-radius: 6px;
            text-align: center;
            cursor: pointer;
            box-sizing: border-box;
            transition: all 0.2s;
        }

        .file-input-btn:hover {
            background-color: #e2e8f0;
            border-color: var(--primary);
        }

        #image-upload {
            display: none;
        }

        .viewer-panel {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .canvas-container {
            display: grid;
            grid-template-columns: 1fr;
            gap: 20px;
        }

        @media (min-width: 640px) {
            .canvas-container {
                grid-template-columns: 1fr 1fr;
            }
        }

        .canvas-box {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 16px;
            text-align: center;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        .canvas-box h3 {
            margin-top: 0;
            margin-bottom: 12px;
            font-size: 1.1rem;
        }

        canvas {
            max-width: 100%;
            height: auto;
            border-radius: 6px;
            background-image: linear-gradient(45deg, #ccc 25%, transparent 25%), 
                              linear-gradient(-45deg, #ccc 25%, transparent 25%), 
                              linear-gradient(45deg, transparent 75%, #ccc 75%), 
                              linear-gradient(-45deg, transparent 75%, #ccc 75%);
            background-size: 20px 20px;
            background-position: 0 0, 0 10px, 10px -10px, -10px 0;
        }

        .stats-panel {
            background: #eff6ff;
            border: 1px solid #bfdbfe;
            border-radius: 12px;
            padding: 16px;
            margin-top: 20px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 16px;
        }

        .stat-item {
            display: flex;
            flex-direction: column;
        }

        .stat-label {
            font-size: 0.85rem;
            color: #1e3a8a;
        }

        .stat-value {
            font-size: 1.25rem;
            font-weight: bold;
            color: var(--primary);
        }

        .explanation {
            margin-top: 30px;
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 24px;
        }

        .explanation h2 {
            margin-top: 0;
            color: var(--primary);
        }

        .explanation ul {
            padding-left: 20px;
        }

        .explanation li {
            margin-bottom: 8px;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>画像のデジタル表現シミュレーター</h1>
        <p class="subtitle">標本化（サンプリング）と量子化（クオンタイズ）を視覚的に体験しよう！</p>
    </header>

    <div class="main-layout">
        <!-- コントロールパネル -->
        <div class="panel">
            <h2>操作コントロール</h2>
            
            <div class="control-group">
                <label for="sampling">① 標本化（解像度）</label>
                <div class="control-desc">画像をどれだけ細かく分割（サンプリング）するかを設定します。</div>
                <input type="range" id="sampling" min="8" max="160" step="4" value="64">
                <div class="value-display">
                    <span>粗い (低解像度)</span>
                    <span id="sampling-val">64 × 64 マス</span>
                    <span>細かい (高解像度)</span>
                </div>
            </div>

            <div class="control-group">
                <label for="quantization">② 量子化（色深度）</label>
                <div class="control-desc">各RGB（赤・緑・青）に何ビット（何段階の色）を割り当てるか設定します。</div>
                <input type="range" id="quantization" min="1" max="8" step="1" value="3">
                <div class="value-display">
                    <span>1 bit (2階調)</span>
                    <span id="quantization-val">各色 3 bit (8階調)</span>
                    <span>8 bit (256階調)</span>
                </div>
            </div>

            <div class="file-input-wrapper">
                <label class="file-input-btn" for="image-upload">
                    自分の画像を使う (ファイルを選択)
                </label>
                <input type="file" id="image-upload" accept="image/*">
            </div>
        </div>

        <!-- プレビュー＆データ量計測 -->
        <div class="viewer-panel">
            <div class="canvas-container">
                <div class="canvas-box">
                    <h3>元の画像 (アナログ状態と仮定)</h3>
                    <canvas id="canvas-original"></canvas>
                </div>
                <div class="canvas-box">
                    <h3>デジタル変換後の画像</h3>
                    <canvas id="canvas-processed"></canvas>
                </div>
            </div>

            <div class="stats-panel">
                <div class="stats-grid">
                    <div class="stat-item">
                        <span class="stat-label">総画素数 (標本化)</span>
                        <span class="stat-value" id="stat-pixels">0 画素</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-label">1画素あたりの情報量</span>
                        <span class="stat-value" id="stat-bpp">0 bit</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-label">論理的な推定データ量</span>
                        <span class="stat-value" id="stat-size">0 KB</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- おまけ解説 -->
    <div class="explanation">
        <h2>💡 「情報Ⅰ」のポイント解説</h2>
        <ul>
            <li><strong>標本化（サンプリング）：</strong> 連続する画像（アナログ）を、格子状のマス目（画素・ピクセル）に区切って取り出すことです。スライダーを左に動かすと、モザイク画のようにカクカクした見た目になります。</li>
            <li><strong>量子化（クオンタイズ）：</strong> 標本化したそれぞれのマスの色を、段階的なデジタル数値に置き換えることです。スライダーを左に動かしてビット数を下げると、使える色が減るためグラデーション部分に境界線（トーンジャンプ）が生まれます。</li>
            <li><strong>デジタル表現とデータ量：</strong> 画素数を増やしたり、ビット数を増やしたりすれば、より実物に近い滑らかな画像になりますが、その分データ量は跳ね上がります（上記の「推定データ量」の計算に注目してみましょう！）。</li>
        </ul>
    </div>
</div>

<script>
    const samplingSlider = document.getElementById('sampling');
    const quantizationSlider = document.getElementById('quantization');
    const samplingVal = document.getElementById('sampling-val');
    const quantizationVal = document.getElementById('quantization-val');
    
    const canvasOriginal = document.getElementById('canvas-original');
    const canvasProcessed = document.getElementById('canvas-processed');
    const ctxOrig = canvasOriginal.getContext('2d');
    const ctxProc = canvasProcessed.getContext('2d');
    
    const imgUpload = document.getElementById('image-upload');
    
    let activeImage = new Image();

    // 初期化：テスト用画像の作成
    function createDefaultImage() {
        const tempCanvas = document.createElement('canvas');
        tempCanvas.width = 400;
        tempCanvas.height = 400;
        const tempCtx = tempCanvas.getContext('2d');
        
        // カラフルなグラデーション背景
        const grad = tempCtx.createLinearGradient(0, 0, 400, 400);
        grad.addColorStop(0, '#ff4e50');
        grad.addColorStop(0.5, '#f9d423');
        grad.addColorStop(1, '#0072ff');
        tempCtx.fillStyle = grad;
        tempCtx.fillRect(0, 0, 400, 400);
        
        // 中央に白い円と青い四角形
        tempCtx.fillStyle = 'rgba(255, 255, 255, 0.8)';
        tempCtx.beginPath();
        tempCtx.arc(200, 200, 100, 0, Math.PI * 2);
        tempCtx.fill();
        
        tempCtx.fillStyle = '#10b981';
        tempCtx.fillRect(150, 150, 100, 100);

        activeImage.src = tempCanvas.toDataURL();
    }

    activeImage.onload = function() {
        // 表示サイズを400x400に統一
        canvasOriginal.width = 400;
        canvasOriginal.height = 400;
        canvasProcessed.width = 400;
        canvasProcessed.height = 400;
        
        // 元画像を描画
        ctxOrig.drawImage(activeImage, 0, 0, 400, 400);
        processImage();
    };

    // アップロード画像の読み込み
    imgUpload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = function(event) {
                activeImage.src = event.target.result;
            };
            reader.readAsDataURL(file);
        }
    });

    // 処理メインロジック
    function processImage() {
        const grid = parseInt(samplingSlider.value);
        const bits = parseInt(quantizationSlider.value);
        
        // スライダー表示の更新
        samplingVal.textContent = `${grid} × ${grid} 画素`;
        const levels = Math.pow(2, bits);
        quantizationVal.textContent = `各色 ${bits} bit (${levels} 階調)`;

        // Canvasのクリア
        ctxProc.clearRect(0, 0, 400, 400);

        // 標本化のサイズ計算
        const blockSize = 400 / grid;
        
        // 量子化ステップ計算
        const step = 255 / (levels - 1);

        // 各マスの処理
        for (let y = 0; y < grid; y++) {
            for (let x = 0; x < grid; x++) {
                // サンプリング（格子の中心座標のピクセルを取得）
                const sampleX = Math.floor(x * blockSize + blockSize / 2);
                const sampleY = Math.floor(y * blockSize + blockSize / 2);
                
                const pixelData = ctxOrig.getImageData(sampleX, sampleY, 1, 1).data;
                let r = pixelData[0];
                let g = pixelData[1];
                let b = pixelData[2];

                // 量子化の計算（各色をビット深度に応じた段階値に丸める）
                r = Math.round(Math.round(r / step) * step);
                g = Math.round(Math.round(g / step) * step);
                b = Math.round(Math.round(b / step) * step);

                // デジタル変換後画像に描画
                ctxProc.fillStyle = `rgb(${r}, ${g}, ${b})`;
                ctxProc.fillRect(x * blockSize, y * blockSize, blockSize + 0.5, blockSize + 0.5);
            }
        }

        // 統計データの計算と表示
        const totalPixels = grid * grid;
        const bpp = bits * 3; // R, G, Bそれぞれ
        const totalBits = totalPixels * bpp;
        const totalBytes = totalBits / 8;
        const totalKB = (totalBytes / 1024).toFixed(2);

        document.getElementById('stat-pixels').textContent = `${totalPixels.toLocaleString()} 画素`;
        document.getElementById('stat-bpp').textContent = `${bpp} bit (RGB 各${bits}bit)`;
        document.getElementById('stat-size').textContent = `${totalKB} KB`;
    }

    // スライダーのイベント設定
    samplingSlider.addEventListener('input', processImage);
    quantizationSlider.addEventListener('input', processImage);

    // 起動
    createDefaultImage();
</script>

</body>
</html>
💡 このアプリで体験できる「授業でのポイント」
標本化（サンプリング）のスライダー

スライダーを左（粗い）に動かすと、グリッドが大きくなってモザイク画のようになります。「画像をどれだけの数の点（画素）に分割するか」が感覚的にわかります。

量子化のスライダー

ビット数を極限まで下げてみてください。例えば「1 bit（2階調、合計8色）」にすると、なだらかだったグラデーションが激しくカクカクした色のブロックに変貌します。デジタル特有の「色を段階的に区切る」という現象が観察できます。

推定データ量の変化

情報Ⅰの試験で頻出の、「画素数 × 1画素あたりのビット数」によるデータ量計算をリアルタイムで自動計算します。高解像度・高ビットにするとデータ量が膨れ上がり、低解像度・低ビットにするとデータ量が非常に小さくなる様子が数字で一目瞭然です。

マイ画像アップロード機能

自分のスマホで撮影した写真などをアップロード（※ブラウザ内だけで処理され、サーバーには送信されません）して試すと、さらに理解が深まり楽しめます。

標本化の際に画素区切りを示す点線を表示して下さい

はい、承知いたしました。
前のコードに、標本化の際に画素区切りを示す点線を表示する機能を追加しました。

以下の新しいコードをすべてコピーして、index.htmlに上書き保存し、ブラウザでリロードして試してみてください。

点線表示機能付きシミュレーター（HTMLコード）
HTML
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>情報Ⅰ：画像のデジタル表現シミュレーター（点線表示付き）</title>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --bg: #f8fafc;
            --card-bg: #ffffff;
            --text: #1e293b;
            --text-light: #64748b;
            --border: #e2e8f0;
        }

        body {
            font-family: 'Helvetica Neue', Arial, 'Hiragino Kaku Gothic ProN', Meiryo, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            margin: 0;
            padding: 20px;
            line-height: 1.6;
        }

        .container {
            max-width: 1100px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
        }

        h1 {
            color: var(--primary);
            margin-bottom: 10px;
        }

        p.subtitle {
            color: var(--text-light);
            font-size: 1.1rem;
        }

        .main-layout {
            display: grid;
            grid-template-columns: 1fr;
            gap: 24px;
        }

        @media (min-width: 768px) {
            .main-layout {
                grid-template-columns: 350px 1fr;
            }
        }

        .panel {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 24px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            height: fit-content;
        }

        .panel h2 {
            font-size: 1.25rem;
            margin-top: 0;
            margin-bottom: 20px;
            border-bottom: 2px solid var(--border);
            padding-bottom: 10px;
        }

        .control-group {
            margin-bottom: 20px;
        }

        .control-group label {
            display: block;
            font-weight: bold;
            margin-bottom: 8px;
        }

        .control-desc {
            font-size: 0.85rem;
            color: var(--text-light);
            margin-bottom: 8px;
            margin-top: -4px;
        }

        input[type="range"] {
            width: 100%;
            margin-bottom: 8px;
            accent-color: var(--primary);
        }

        .value-display {
            display: flex;
            justify-content: space-between;
            font-size: 0.9rem;
            font-weight: bold;
            color: var(--primary);
        }

        .checkbox-group {
            display: flex;
            align-items: center;
            margin-top: 10px;
        }

        .checkbox-group input {
            margin-right: 8px;
        }

        .file-input-wrapper {
            margin-top: 15px;
        }

        .file-input-btn {
            display: inline-block;
            width: 100%;
            padding: 10px;
            background-color: #f1f5f9;
            border: 2px dashed #cbd5e1;
            border-radius: 6px;
            text-align: center;
            cursor: pointer;
            box-sizing: border-box;
            transition: all 0.2s;
        }

        .file-input-btn:hover {
            background-color: #e2e8f0;
            border-color: var(--primary);
        }

        #image-upload {
            display: none;
        }

        .viewer-panel {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .canvas-container {
            display: grid;
            grid-template-columns: 1fr;
            gap: 20px;
        }

        @media (min-width: 640px) {
            .canvas-container {
                grid-template-columns: 1fr 1fr;
            }
        }

        .canvas-box {
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 16px;
            text-align: center;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }

        .canvas-box h3 {
            margin-top: 0;
            margin-bottom: 12px;
            font-size: 1.1rem;
        }

        canvas {
            max-width: 100%;
            height: auto;
            border-radius: 6px;
            background-image: linear-gradient(45deg, #ccc 25%, transparent 25%), 
                              linear-gradient(-45deg, #ccc 25%, transparent 25%), 
                              linear-gradient(45deg, transparent 75%, #ccc 75%), 
                              linear-gradient(-45deg, transparent 75%, #ccc 75%);
            background-size: 20px 20px;
            background-position: 0 0, 0 10px, 10px -10px, -10px 0;
        }

        .stats-panel {
            background: #eff6ff;
            border: 1px solid #bfdbfe;
            border-radius: 12px;
            padding: 16px;
            margin-top: 20px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 16px;
        }

        .stat-item {
            display: flex;
            flex-direction: column;
        }

        .stat-label {
            font-size: 0.85rem;
            color: #1e3a8a;
        }

        .stat-value {
            font-size: 1.25rem;
            font-weight: bold;
            color: var(--primary);
        }

        .explanation {
            margin-top: 30px;
            background: var(--card-bg);
            border: 1px solid var(--border);
            border-radius: 12px;
            padding: 24px;
        }

        .explanation h2 {
            margin-top: 0;
            color: var(--primary);
        }

        .explanation ul {
            padding-left: 20px;
        }

        .explanation li {
            margin-bottom: 8px;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>画像のデジタル表現シミュレーター</h1>
        <p class="subtitle">標本化（サンプリング）と量子化（クオンタイズ）を視覚的に体験しよう！</p>
    </header>

    <div class="main-layout">
        <!-- コントロールパネル -->
        <div class="panel">
            <h2>操作コントロール</h2>
            
            <div class="control-group">
                <label for="sampling">① 標本化（解像度）</label>
                <div class="control-desc">画像をどれだけ細かく分割（サンプリング）するかを設定します。</div>
                <input type="range" id="sampling" min="8" max="160" step="4" value="64">
                <div class="value-display">
                    <span>粗い (低解像度)</span>
                    <span id="sampling-val">64 × 64 マス</span>
                    <span>細かい (高解像度)</span>
                </div>
                <!-- 点線表示用チェックボックス -->
                <div class="checkbox-group">
                    <input type="checkbox" id="show-lines" checked>
                    <label for="show-lines" style="font-weight: normal; margin-bottom: 0;">画素の区切り線を表示する</label>
                </div>
            </div>

            <div class="control-group">
                <label for="quantization">② 量子化（色深度）</label>
                <div class="control-desc">各RGB（赤・緑・青）に何ビット（何段階の色）を割り当てるか設定します。</div>
                <input type="range" id="quantization" min="1" max="8" step="1" value="3">
                <div class="value-display">
                    <span>1 bit (2階調)</span>
                    <span id="quantization-val">各色 3 bit (8階調)</span>
                    <span>8 bit (256階調)</span>
                </div>
            </div>

            <div class="file-input-wrapper">
                <label class="file-input-btn" for="image-upload">
                    自分の画像を使う (ファイルを選択)
                </label>
                <input type="file" id="image-upload" accept="image/*">
            </div>
        </div>

        <!-- プレビュー＆データ量計測 -->
        <div class="viewer-panel">
            <div class="canvas-container">
                <div class="canvas-box">
                    <h3>元の画像 (アナログ状態と仮定)</h3>
                    <canvas id="canvas-original"></canvas>
                </div>
                <div class="canvas-box">
                    <h3>デジタル変換後の画像</h3>
                    <canvas id="canvas-processed"></canvas>
                </div>
            </div>

            <div class="stats-panel">
                <div class="stats-grid">
                    <div class="stat-item">
                        <span class="stat-label">総画素数 (標本化)</span>
                        <span class="stat-value" id="stat-pixels">0 画素</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-label">1画素あたりの情報量</span>
                        <span class="stat-value" id="stat-bpp">0 bit</span>
                    </div>
                    <div class="stat-item">
                        <span class="stat-label">論理的な推定データ量</span>
                        <span class="stat-value" id="stat-size">0 KB</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- おまけ解説 -->
    <div class="explanation">
        <h2>💡 「情報Ⅰ」のポイント解説</h2>
        <ul>
            <li><strong>標本化（サンプリング）：</strong> 連続する画像（アナログ）を、格子状のマス目（画素・ピクセル）に区切って取り出すことです。点線をONにすると、その区切りが視覚的にわかります。スライダーを左に動かすと、モザイク画のようにカクカクした見た目になります。</li>
            <li><strong>量子化（クオンタイズ）：</strong> 標本化したそれぞれのマスの色を、段階的なデジタル数値に置き換えることです。スライダーを左に動かしてビット数を下げると、使える色が減るためグラデーション部分に境界線（トーンジャンプ）が生まれます。</li>
            <li><strong>デジタル表現とデータ量：</strong> 画素数を増やしたり、ビット数を増やしたりすれば、より実物に近い滑らかな画像になりますが、その分データ量は跳ね上がります（上記の「推定データ量」の計算に注目してみましょう！）。</li>
        </ul>
    </div>
</div>

<script>
    const samplingSlider = document.getElementById('sampling');
    const quantizationSlider = document.getElementById('quantization');
    const samplingVal = document.getElementById('sampling-val');
    const quantizationVal = document.getElementById('quantization-val');
    const showLinesCheckbox = document.getElementById('show-lines');
    
    const canvasOriginal = document.getElementById('canvas-original');
    const canvasProcessed = document.getElementById('canvas-processed');
    const ctxOrig = canvasOriginal.getContext('2d');
    const ctxProc = canvasProcessed.getContext('2d');
    
    const imgUpload = document.getElementById('image-upload');
    
    let activeImage = new Image();

    // 初期化：テスト用画像の作成
    function createDefaultImage() {
        const tempCanvas = document.createElement('canvas');
        tempCanvas.width = 400;
        tempCanvas.height = 400;
        const tempCtx = tempCanvas.getContext('2d');
        
        // カラフルなグラデーション背景
        const grad = tempCtx.createLinearGradient(0, 0, 400, 400);
        grad.addColorStop(0, '#ff4e50');
        grad.addColorStop(0.5, '#f9d423');
        grad.addColorStop(1, '#0072ff');
        tempCtx.fillStyle = grad;
        tempCtx.fillRect(0, 0, 400, 400);
        
        // 中央に白い円と青い四角形
        tempCtx.fillStyle = 'rgba(255, 255, 255, 0.8)';
        tempCtx.beginPath();
        tempCtx.arc(200, 200, 100, 0, Math.PI * 2);
        tempCtx.fill();
        
        tempCtx.fillStyle = '#10b981';
        tempCtx.fillRect(150, 150, 100, 100);

        activeImage.src = tempCanvas.toDataURL();
    }

    activeImage.onload = function() {
        // 表示サイズを400x400に統一
        canvasOriginal.width = 400;
        canvasOriginal.height = 400;
        canvasProcessed.width = 400;
        canvasProcessed.height = 400;
        
        // 元画像を描画
        ctxOrig.drawImage(activeImage, 0, 0, 400, 400);
        processImage();
    };

    // アップロード画像の読み込み
    imgUpload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (file) {
            const reader = new FileReader();
            reader.onload = function(event) {
                activeImage.src = event.target.result;
            };
            reader.readAsDataURL(file);
        }
    });

    // 処理メインロジック
    function processImage() {
        const grid = parseInt(samplingSlider.value);
        const bits = parseInt(quantizationSlider.value);
        
        // スライダー表示の更新
        samplingVal.textContent = `${grid} × ${grid} 画素`;
        const levels = Math.pow(2, bits);
        quantizationVal.textContent = `各色 ${bits} bit (${levels} 階調)`;

        // Canvasのクリア
        ctxProc.clearRect(0, 0, 400, 400);

        // 標本化のサイズ計算
        const blockSize = 400 / grid;
        
        // 量子化ステップ計算
        const step = 255 / (levels - 1);

        // 各マスの処理
        for (let y = 0; y < grid; y++) {
            for (let x = 0; x < grid; x++) {
                // サンプリング（格子の中心座標のピクセルを取得）
                const sampleX = Math.floor(x * blockSize + blockSize / 2);
                const sampleY = Math.floor(y * blockSize + blockSize / 2);
                
                const pixelData = ctxOrig.getImageData(sampleX, sampleY, 1, 1).data;
                let r = pixelData[0];
                let g = pixelData[1];
                let b = pixelData[2];

                // 量子化の計算（各色をビット深度に応じた段階値に丸める）
                r = Math.round(Math.round(r / step) * step);
                g = Math.round(Math.round(g / step) * step);
                b = Math.round(Math.round(b / step) * step);

                // デジタル変換後画像に描画
                ctxProc.fillStyle = `rgb(${r}, ${g}, ${b})`;
                ctxProc.fillRect(x * blockSize, y * blockSize, blockSize, blockSize);
            }
        }

        // --- 画素の区切り線（点線）を描画 ---
        if (showLinesCheckbox.checked) {
            ctxProc.strokeStyle = 'rgba(128, 128, 128, 0.5)'; // 半透明のグレー
            ctxProc.lineWidth = 1;
            ctxProc.setLineDash([2, 2]); // 2px描画して2px開ける点線

            // 縦線
            for (let x = 0; x <= grid; x++) {
                ctxProc.beginPath();
                ctxProc.moveTo(x * blockSize, 0);
                ctxProc.lineTo(x * blockSize, 400);
                ctxProc.stroke();
            }
            // 横線
            for (let y = 0; y <= grid; y++) {
                ctxProc.beginPath();
                ctxProc.moveTo(0, y * blockSize);
                ctxProc.lineTo(400, y * blockSize);
                ctxProc.stroke();
            }
            // 点線の設定をリセット
            ctxProc.setLineDash([]);
        }
        // ------------------------------------

        // 統計データの計算と表示
        const totalPixels = grid * grid;
        const bpp = bits * 3; // R, G, Bそれぞれ
        const totalBits = totalPixels * bpp;
        const totalBytes = totalBits / 8;
        const totalKB = (totalBytes / 1024).toFixed(2);

        document.getElementById('stat-pixels').textContent = `${totalPixels.toLocaleString()} 画素`;
        document.getElementById('stat-bpp').textContent = `${bpp} bit (RGB 各${bits}bit)`;
        document.getElementById('stat-size').textContent = `${totalKB} KB`;
    }

    // イベント設定
    samplingSlider.addEventListener('input', processImage);
    quantizationSlider.addEventListener('input', processImage);
    showLinesCheckbox.addEventListener('change', processImage);

    // 起動
    createDefaultImage();
</script>

</body>
</html>
追加機能の説明
「画素の区切り線を表示する」チェックボックス

①標本化のコントロールの下に追加しました。

チェックを入れると、デジタル変換後の画像上に、画素の区切りを示すグレーの点線が表示されます。標本化によって画像が「格子状のマス目（画素）」に分割されていることが、視覚的にさらにわかりやすくなります。

点線の動的な描画

標本化のスライダーを動かして解像度（画素数）を変えると、それに合わせて点線の間隔も自動的に変わります。

標本化を粗く（画素数を少なく）するとマス目が大きくなり、細かく（画素数を多く）するとマス目が小さくなる様子が、点線によって強調されます。

「情報Ⅰ」の授業や自習で、デジタル画像の仕組みを学ぶのにぜひお役立てください！

readme.mdファイルを作成して下さい

💡 「情報Ⅰ」における学習ポイント
標本化（サンプリング）: 連続的な情報（アナログ画像）を、一定の間隔（格子状の画素）で区切って取り出す操作。

量子化（クオンタイズ）: 取り出した色情報を、段階的な数値（ビット値）に丸めて表現する操作。

デジタルデータ量: 解像度（画素数）を2倍、色深度を2倍にすると、データ量がどのように変化するかを考察する教材として最適です。

📄 ライセンス
MIT License
自由に変更・再配布・授業等でのご活用が可能です。
"""

with open("README.md", "w", encoding="utf-8") as f:
f.write(readme_content)

print("README.md generated successfully.")


```text?code_stdout&code_event_index=1
README.md generated successfully.

README.md ファイルを作成しました。
MD のアイコン
README
 MD 
開く

以下は作成した README.md の内容です。プロジェクトの概要、使い方、学習ポイントなどをまとめた標準的なフォーマットになっています。

Markdown
# 画像のデジタル表現シミュレーター (情報Ⅰ 対応)

高校科目「情報Ⅰ」における**画像のデジタル表現（標本化・量子化）**を直感的に学習・体験するためのインタラクティブWebアプリケーションです。

---

## 🌟 主な機能

1. **標本化（サンプリング）シミュレーション**
   - 画面の分割（解像度）をスライダーで自由に変更可能（8×8 ～ 160×160画素）。
   - 画素の区切りを示す**点線のグリッド表示/非表示**を切り替え可能。
   - 標本化（格子状に細かく区切るプロセス）を視覚的に体験できます。

2. **量子化（クオンタイズ）シミュレーション**
   - 各色（R, G, B）に割り当てるビット数（1 bit ～ 8 bit）をスライダーで調整可能。
   - 階調の変化や、ビット数が低い時に発生するトーンジャンプ（マッハバンド効果）をリアルタイムに確認できます。

3. **推定データ量の自動計算**
   - 設定した解像度およびビット深度から、論理的な画像データ量を自動算出します。
   - 計算式: `総画素数 × 1画素あたりのビット数 (R, G, B 各N bit = 3N bit)`
   - 画素数や色深度とデータ量の関係性を数値で実感できます。

4. **オリジナル画像のアップロード機能**
   - お手持ちの画像（PNG, JPEGなど）を取り込んでシミュレーションできます。
   - ※処理はすべてブラウザ内で行われるため、外部サーバーへの画像送信はなく安心です。

---

## 🚀 使い方

1. 本リポジトリの `index.html` をダウンロード（またはコードをコピー）します。
2. 保存した `index.html` をお持ちのWebブラウザ（Chrome, Edge, Safari, Firefoxなど）で開きます。
   - 特別な環境構築やサーバーのセットアップ、インストール作業は一切不要です。
3. 左側のコントロールパネルから各スライダーを操作して、標本化や量子化の変化を体験してください。

---

## 📂 ファイル構成

```text
.
├── index.html    # メインのHTML/CSS/JavaScript（単体で動作可能）
└── README.md     # 本ファイル
💡 「情報Ⅰ」における学習ポイント
標本化（サンプリング）: 連続的な情報（アナログ画像）を、一定の間隔（格子状の画素）で区切って取り出す操作。

量子化（クオンタイズ）: 取り出した色情報を、段階的な数値（ビット値）に丸めて表現する操作。

デジタルデータ量: 解像度（画素数）を2倍、色深度を2倍にすると、データ量がどのように変化するかを考察する教材として最適です。

📄 ライセンス
MIT License
自由に変更・再配布・授業等でのご活用が可能です。




富山県立学校 のチャットはモデルのトレーニングには使用されません。Gemini は AI であり、間違えることがあります。プライバシーと Gemini新しいウィンドウで開く

# 画像のデジタル表現シミュレーター (情報Ⅰ 対応)

高校科目「情報Ⅰ」における**画像のデジタル表現（標本化・量子化）**を直感的に学習・体験するためのインタラクティブWebアプリケーションです。

---

## 🌟 主な機能

1. **標本化（サンプリング）シミュレーション**
   - 画面の分割（解像度）をスライダーで自由に変更可能（8×8 ～ 160×160画素）。
   - 画素の区切りを示す**点線のグリッド表示/非表示**を切り替え可能。
   - 標本化（格子状に細かく区切るプロセス）を視覚的に体験できます。

2. **量子化（クオンタイズ）シミュレーション**
   - 各色（R, G, B）に割り当てるビット数（1 bit ～ 8 bit）をスライダーで調整可能。
   - 階調の変化や、ビット数が低い時に発生するトーンジャンプ（マッハバンド効果）をリアルタイムに確認できます。

3. **推定データ量の自動計算**
   - 設定した解像度およびビット深度から、論理的な画像データ量を自動算出します。
   - 計算式: `総画素数 × 1画素あたりのビット数 (R, G, B 各N bit = 3N bit)`
   - 画素数や色深度とデータ量の関係性を数値で実感できます。

4. **オリジナル画像のアップロード機能**
   - お手持ちの画像（PNG, JPEGなど）を取り込んでシミュレーションできます。
   - ※処理はすべてブラウザ内で行われるため、外部サーバーへの画像送信はなく安心です。

---

## 🚀 使い方

1. 本リポジトリの `index.html` をダウンロード（またはコードをコピー）します。
2. 保存した `index.html` をお持ちのWebブラウザ（Chrome, Edge, Safari, Firefoxなど）で開きます。
   - 特別な環境構築やサーバーのセットアップ、インストール作業は一切不要です。
3. 左側のコントロールパネルから各スライダーを操作して、標本化や量子化の変化を体験してください。

---

## 📂 ファイル構成

```text
.
├── index.html    # メインのHTML/CSS/JavaScript（単体で動作可能）
└── README.md     # 本ファイル
```

---

## 💡 「情報Ⅰ」における学習ポイント

- **標本化（サンプリング）**: 連続的な情報（アナログ画像）を、一定の間隔（格子状の画素）で区切って取り出す操作。
- **量子化（クオンタイズ）**: 取り出した色情報を、段階的な数値（ビット値）に丸めて表現する操作。
- **デジタルデータ量**: 解像度（画素数）を2倍、色深度を2倍にすると、データ量がどのように変化するかを考察する教材として最適です。

---

## 📄 ライセンス

MIT License
自由に変更・再配布・授業等でのご活用が可能です。
