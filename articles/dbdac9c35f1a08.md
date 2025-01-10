---
title: "DifyのText to Speechで音声生成したけどダウンロードできない人へ"
emoji: "🐥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: true
---

# DifyのText to Speechで音声生成したけどダウンロードできない人へ

DifyのText to Speechで音声生成したけどダウンロードできない人へ

ドキュメントがなく解決に1時間くらいかかったので、同じような人のために書いておきます。

# ワークフロー

テキスト情報を入力→ Text to Speechで音声生成→ Outputをそのまま出力するワークフローを想定しています。


![](https://storage.googleapis.com/zenn-user-upload/8a6b46865660-20250110.png)


環境構築は省略しますが、ローカルでDockerを使って動かすことを前提としています。

APIとして公開した後、以下のソースコードで音声の取得（ダウンロード）が可能です。

```python
import requests


api_url = "http://localhost/v1/workflows/run"
api_key = "app-xxxxxx"

headers = {
    "Authorization": f"Bearer {api_key}",
    "Content-Type": "application/json"
}

data = {
    "inputs": {
        "message": "Hello, Dify!"
    },
    "user": "user"
}

response = requests.post(api_url, headers=headers, json=data)

if response.status_code == 200:
    print("Success!")
    print("Response:", response.json())
else:
    print("Error:", response.status_code)
    print("Response:", response.text)

response_json = response.json()
audio_url = response_json["data"]["outputs"]["files"][0]["url"]
print("Audio URL:", audio_url)

audio_url = f"http://localhost{audio_url}"

response = requests.get(audio_url)
with open("audio.wav", "wb") as f:
    f.write(response.content)
print("Audio file saved as audio.wav")
```

もう少し詳しく説明すると、出力は以下のような形式になります。

```json
{
  "data": {
    "outputs": {
      "files": [
        {
          "url": "/files/tools/xxxx.wav?timestamp=xxxx&nonce=xxxx&sign=xxxx"
        }
      ]
    }
  }
}
```

よってURLは、http://localhost/files/tools/xxxx.wav?timestamp=xxxx&nonce=xxxx&sign=xxxx となります。

このURLにGETリクエストを送ることで、音声ファイルを取得することができました。





