## FEATURE:

- 別のPydantic AIエージェントをツールとして持つPydantic AIエージェント。
- プライマリエージェント用のリサーチエージェントと、サブエージェント用のメール下書きエージェント。
- エージェントと対話するためのCLI。
- メール下書きエージェント用のGmail、リサーチエージェント用のBrave API。

## EXAMPLES:

`examples/`フォルダには、例の内容を理解し、上記機能のドキュメントを作成する際の独自のREADMEの構造方法を学ぶためのREADMEがあります。

- examples/cli.py - CLIを作成するためのテンプレートとして使用
- examples/agent/ - ここにあるすべてのファイルを読んで、異なるプロバイダーとLLMをサポートし、エージェントの依存関係を処理し、エージェントにツールを追加するPydantic AIエージェント作成のベストプラクティスを理解してください。

これらの例を直接コピーしないでください。これはまったく別のプロジェクト用です。ただし、インスピレーションとベストプラクティスのために使用してください。

## DOCUMENTATION:

Pydantic AIドキュメント: https://ai.pydantic.dev/

## OTHER CONSIDERATIONS:

- .env.example、GmailとBraveの設定方法を含むセットアップ手順を記載したREADMEを含める。
- READMEにプロジェクト構造を含める。
- 仮想環境は必要な依存関係とともにすでにセットアップされています。
- 環境変数にはpython_dotenvとload_env()を使用する