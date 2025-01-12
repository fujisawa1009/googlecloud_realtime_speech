# リアルタイム音声

# googlecloud_realtime_speech
参考リポジトリ
https://github.com/GoogleCloudPlatform/python-docs-samples/blob/main/speech/microphone/transcribe_streaming_infinite.py

[Google Cloud の認証情報JSONファイル取得手順]
Google Cloud コンソールにログイン
プロジェクトを選択  [speech recognition]
左側のメニューから「IAMと管理」→「サービスアカウント」を選択します。

「サービスアカウントを作成」をクリックするか、既存のサービスアカウントを選択します。
サービスアカウントの詳細画面で「キー」タブをクリックします。
「鍵を追加」→「新しい鍵を作成」を選択します。
キーのタイプとして「JSON」を選択し、「作成」をクリックします。

speech-recognition-447614-34617aeea7a3.json

JSONファイルが自動的にダウンロードされます。このファイルには秘密鍵情報が含まれているため、安全に保管してください

--
# pythonインストール
brew install python

# パス追加とアップグレード
export PATH="/usr/local/opt/python/libexec/bin:$PATH"
pip3 install --upgrade pip

# pythonのバージョン確認
python --version
> Python 3.9.6

# 仮想環境を作成
python -m venv speech_env
source speech_env/bin/activate

# 必要なライブラリをインストール
pip3 install google-cloud-speech pyaudio
pip install google-cloud-speech pyaudio

# Google Cloud の設定
Google Cloud プロジェクトを作成し、Speech-to-Text APIを有効にする
サービスアカウントキーを作成し、JSONファイルをダウンロード
環境変数を設定
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-file.json"

# PyAudioのインストール Homebrewを使ってPortAudioをインストール
brew install portaudio

# PyAudioをインストール
pip install --global-option='build_ext' --global-option='-I/opt/homebrew/Cellar/portaudio/19.7.0/include' --global-option='-L/opt/homebrew/Cellar/portaudio/19.7.0/lib' pyaudio

# コードの実行 コードをPythonファイル（例: speech_recognition.py）に保存
# python speech_recognition.py
python3 speech_recognition.py

# これらの手順を踏むことで、ローカルのMacBookでこのコードを動かすことができます。音声認識が開始され、"Quit"または"Exit"と言うまで継続する

[不足していた手順]
python3 -m venv speech_env
source speech_env/bin/activate
pip install google-cloud-speech

pip3 install --global-option='build_ext' --global-option='-I/opt/homebrew/Cellar/portaudio/19.7.0/include' --global-option='-L/opt/homebrew/Cellar/portaudio/19.7.0/lib' pyaudio
[実行]
python3 speech_recognition.py

※google側の無料トライアル終了している：エラー内容はGoogle Cloud認証情報ファイルが見つからない
＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝
--以下でdocker化しようとした手順
# Docker化したので以下の手順だけで良い
Google Cloud の認証情報JSONファイルを取得し、プロジェクトディレクトリに配置します。
ファイル名はgoogle_credentials.jsonとする

# Dockerイメージをビルド
docker build -t speech_recognition .

# Dockerコンテナを実行前に必要
brew install pulseaudio

# PulseAudioデーモンを起動
pulseaudio --load=module-native-protocol-tcp --exit-idle-time=-1 --daemon

# Dockerfileを修正して、PulseAudioクライアントをインストール
RUN apt-get update && apt-get install -y pulseaudio

# コンテナ起動時に以下のオプションを追加
docker run -it \
  -e PULSE_SERVER=docker.for.mac.localhost \
  -v ~/.config/pulse:/root/.config/pulse \
  -e GOOGLE_APPLICATION_CREDENTIALS=/app/google_credentials.json \
  speech_recognition


※ホストマシンの音声デバイスをコンテナにマウントするために--device /dev/snd:/dev/sndオプションを使用
※Google Cloud認証情報を環境変数として渡す
※PyAudioはホストマシンの音声デバイスに直接アクセスするため、Dockerコンテナ内での動作に制限がある場合がある

