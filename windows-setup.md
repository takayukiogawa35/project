以下の手順を最初から最後まで全部自動で実行してください。途中で止まらず、全て完了してから報告してください。日本語で応答してください。
この手順は Windows 用です。

【重要ルール】
- どのステップでエラーが出ても、自分で原因を分析して修正し、そのステップをやり直してから次に進んでください。絶対に途中で止まらないでください。
- Pythonを実行するときは必ず ./venv/Scripts/python.exe を使ってください（venvのフルパス指定）。pip も ./venv/Scripts/python.exe -m pip の形で実行してください。venv\Scripts\activate は使わないでください。
- この書き方（スラッシュ区切り）は PowerShell でも Git Bash でも動きます。
- winget でインストールした直後は PATH（コマンドの場所の登録）が反映されないことがあります。コマンドが見つからない場合は、PATH を再読み込みするか、インストール先のフルパスで実行してください。
- 全ステップ完了後に「環境構築が完了しました」と報告してください。途中報告は不要です。

■ 1. システムツールの確認とインストール

1a. winget（Homebrew の代わり）:
winget --version で確認。なければ Microsoft Store の「アプリ インストーラー」を更新・インストールするようユーザーに案内してください（これだけは手作業が必要です）。

1b. Python 3:
py -3.12 --version で確認。なければ:
winget install -e --id Python.Python.3.12 --accept-package-agreements --accept-source-agreements
注意: python と打つと Microsoft Store が開く「ダミーの python」がある場合があります。必ず py -3.12 を使ってください。

1c. ffmpeg:
ffmpeg -version で確認。なければ:
winget install -e --id Gyan.FFmpeg --accept-package-agreements --accept-source-agreements
インストール後に ffmpeg -version が通らない場合は、PATH を再読み込みしてから再確認してください（Whisper は ffmpeg が PATH 上にないと動きません）。

1d. Node.js:
node --version で確認。バージョン18以上がすでに入っていればスキップ。なければ:
winget install -e --id OpenJS.NodeJS.LTS --accept-package-agreements --accept-source-agreements

■ 2. Python仮想環境の作成とパッケージインストール
py -3.12 -m venv venv
./venv/Scripts/python.exe -m pip install --upgrade pip
./venv/Scripts/python.exe -m pip install openai-whisper torch torchaudio silero-vad budoux

（NVIDIA製のGPUがあるPCで高速化したい場合は、torch と torchaudio を https://pytorch.org の案内に沿った CUDA 版で入れ直してください。なくても動作します）

インストール後、以下でインポート確認:
./venv/Scripts/python.exe -c "import whisper; import torch; import budoux; from silero_vad import load_silero_vad; print('OK')"
エラーが出た場合は ./venv/Scripts/python.exe -m pip install をやり直してください。

■ 3. Whisperモデルの事前ダウンロード（約3GB、時間がかかります）
./venv/Scripts/python.exe -c "import whisper; whisper.load_model('large-v3'); print('Whisper model downloaded')"
メモリ不足エラーが出た場合は large-v3 の代わりに medium を使ってください:
./venv/Scripts/python.exe -c "import whisper; whisper.load_model('medium'); print('Whisper medium model downloaded')"
（モデルは %USERPROFILE%\.cache\whisper に保存されます）

■ 4. Remotionプロジェクトのセットアップ
対話形式を避けるため、以下で作成してください:
npx -y create-video@latest --yes --blank remotion-project
（パッケージマネージャーは npm を使ってください）

作成後:
cd remotion-project
npm install budoux @remotion/google-fonts
npx -y remotion upgrade
npx -y skills add remotion-dev/skills --yes
（Agent Skillsの質問が出たら全てデフォルトで進めてください）
cd ..

注意: Windows ではシンボリックリンク（ファイルの別名）の作成が制限されている場合があり、.claude/skills が空になることがあります。その場合は .agents/skills の中身を remotion-project/.claude/skills にコピーしてください。

セットアップ後、remotion-project/node_modules/ が存在することを確認してください。

■ 5. 作業フォルダの作成
OneDrive が有効でも正しい「ドキュメント」に作れるよう、以下で作成してください:
powershell -NoProfile -Command "$d=[Environment]::GetFolderPath('MyDocuments'); New-Item -ItemType Directory -Force -Path \"$d\video-edit\input\", \"$d\video-edit\output\""

■ 6. 最終確認
以下を全て確認してください：
- ドキュメント\video-edit\input フォルダが存在する（実際のフルパスも報告してください）
- ドキュメント\video-edit\output フォルダが存在する
- ./venv/Scripts/python.exe -c "import whisper; import torch; import budoux; from silero_vad import load_silero_vad; print('ALL OK')" が成功する
- ffmpeg -version が成功する
- remotion-project/node_modules/@remotion/google-fonts が存在する
- remotion-project/.claude/skills/ に Remotion のスキルが入っている（Agent Skills）

全て問題なければ「環境構築が完了しました」と報告してください。
問題がある場合は自分で修正してから報告してください。
