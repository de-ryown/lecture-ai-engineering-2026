# Gemini API の操作メモ

## 利用可能なモデルを確認する

```bash
uv run python -c "from google import genai; from app.config import get_api_key; c = genai.Client(api_key=get_api_key()); [print(m.name) for m in c.models.list() if 'flash' in m.name]"
```

- `app.config` を読む
- `if 'flash' in m.name` を消すとすべてのモデルが出る
- 名前に `-tts` / `-image` / `-native-audio` / `-live` が付くものは、テキストで質問する用途ではない

## モデルを切り替える

```bash
AIE_MODEL=gemini-3.5-flash uv run python -m app.ask "質問"
```

- `app/config.py` が環境変数 `AIE_MODEL` を読む。指定しなければ `gemini-3.5-flash-lite`

## temperature を変える

`generate_content` に `config=types.GenerateContentConfig(temperature=...)` を渡す。

```python
from google.genai import types
config = types.GenerateContentConfig(temperature=0.5)
response = client.models.generate_content(model=MODEL, contents=question, config=config)
```

- 範囲は 0.0〜2.0。0 にしても、毎回同じ出力になるとは限らない
- lec01 では `ask.py` に `--temperature` を追加した：`uv run python -m app.ask --temperature 0 "Question"`

## トークン数を取る

- `response.usage_metadata.prompt_token_count`：入力トークン数
- `response.usage_metadata.candidates_token_count`：出力トークン数
- `usage_metadata` が None のこともあるので、確かめてから使う
