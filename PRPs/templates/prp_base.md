name: "Base PRP Template v2 - Context-Rich with Validation Loops"
description: |

## Purpose
AIエージェントが反復的な改良を通じて動作するコードを実現するための、十分なコンテキストと自己検証機能を備えた機能実装に最適化されたテンプレート。

## Core Principles
1. **Context is King**: 必要なすべてのドキュメント、例、注意事項を含める
2. **Validation Loops**: AIが実行して修正できる実行可能なテスト/リントを提供
3. **Information Dense**: コードベースのキーワードとパターンを使用
4. **Progressive Success**: シンプルに始めて、検証し、その後強化
5. **Global rules**: CLAUDE.mdのすべてのルールに従うこと

---

## Goal
[何を構築する必要があるか - 最終状態と要望について具体的に]

## Why
- [ビジネス価値とユーザーへの影響]
- [既存機能との統合]
- [これが解決する問題と誰のために]

## What
[ユーザーに見える動作と技術要件]

### Success Criteria
- [具体的で測定可能な成果]

## All Needed Context

### Documentation & References (list all context needed to implement the feature)
```yaml
# MUST READ - Include these in your context window
- url: [公式APIドキュメントURL]
  why: [必要な特定のセクション/メソッド]

- file: [path/to/example.py]
  why: [従うべきパターン、避けるべき落とし穴]

- doc: [ライブラリドキュメントURL]
  section: [よくある落とし穴についての特定のセクション]
  critical: [一般的なエラーを防ぐ重要な洞察]

- docfile: [PRPs/ai_docs/file.md]
  why: [ユーザーがプロジェクトに貼り付けたドキュメント]

```

### Current Codebase tree (run `tree` in the root of the project) to get an overview of the codebase
```bash

```

### Desired Codebase tree with files to be added and responsibility of file
```bash

```

### Known Gotchas of our codebase & Library Quirks

```python
# CRITICAL: [Library name] requires [specific setup]
# Example: FastAPI requires async functions for endpoints
# Example: This ORM doesn't support batch inserts over 1000 records
# Example: We use pydantic v2 and  
```

## Implementation Blueprint

### Data models and structure

コアデータモデルを作成し、型安全性と一貫性を確保します。
```python
Examples: 
 - orm models
 - pydantic models
 - pydantic schemas
 - pydantic validators

```

### list of tasks to be completed to fullfill the PRP in the order they should be completed

```yaml
Task 1:
MODIFY src/existing_module.py:
  - FIND pattern: "class OldImplementation"
  - INJECT after line containing "def __init__"
  - PRESERVE existing method signatures

CREATE src/new_feature.py:
  - MIRROR pattern from: src/similar_feature.py
  - MODIFY class name and core logic
  - KEEP error handling pattern identical

...(...)

Task N:
...

```

### Per task pseudocode as needed added to each task
```python

# Task 1
# Pseudocode with CRITICAL details dont write entire code
async def new_feature(param: str) -> Result:
    # PATTERN: Always validate input first (see src/validators.py)
    validated = validate_input(param)  # raises ValidationError
    
    # GOTCHA: This library requires connection pooling
    async with get_connection() as conn:  # see src/db/pool.py
        # PATTERN: Use existing retry decorator
        @retry(attempts=3, backoff=exponential)
        async def _inner():
            # CRITICAL: API returns 429 if >10 req/sec
            await rate_limiter.acquire()
            return await external_api.call(validated)
        
        result = await _inner()
    
    # PATTERN: Standardized response format
    return format_response(result)  # see src/utils/responses.py
```

### Integration Points
```yaml
DATABASE:
  - migration: "Add column 'feature_enabled' to users table"
  - index: "CREATE INDEX idx_feature_lookup ON users(feature_id)"
  
CONFIG:
  - add to: config/settings.py
  - pattern: "FEATURE_TIMEOUT = int(os.getenv('FEATURE_TIMEOUT', '30'))"
  
ROUTES:
  - add to: src/api/routes.py  
  - pattern: "router.include_router(feature_router, prefix='/feature')"
```

## Validation Loop

### Level 1: Syntax & Style
```bash
# Run these FIRST - fix any errors before proceeding
ruff check src/new_feature.py --fix  # Auto-fix what's possible
mypy src/new_feature.py              # Type checking

# Expected: No errors. If errors, READ the error and fix.
```

### Level 2: Unit Tests each new feature/file/function use existing test patterns
```python
# CREATE test_new_feature.py with these test cases:
def test_happy_path():
    """Basic functionality works"""
    result = new_feature("valid_input")
    assert result.status == "success"

def test_validation_error():
    """Invalid input raises ValidationError"""
    with pytest.raises(ValidationError):
        new_feature("")

def test_external_api_timeout():
    """Handles timeouts gracefully"""
    with mock.patch('external_api.call', side_effect=TimeoutError):
        result = new_feature("valid")
        assert result.status == "error"
        assert "timeout" in result.message
```

```bash
# Run and iterate until passing:
uv run pytest test_new_feature.py -v
# If failing: Read error, understand root cause, fix code, re-run (never mock to pass)
```

### Level 3: Integration Test
```bash
# Start the service
uv run python -m src.main --dev

# Test the endpoint
curl -X POST http://localhost:8000/feature \
  -H "Content-Type: application/json" \
  -d '{"param": "test_value"}'

# Expected: {"status": "success", "data": {...}}
# If error: Check logs at logs/app.log for stack trace
```

## Final validation Checklist
- [ ] All tests pass: `uv run pytest tests/ -v`
- [ ] No linting errors: `uv run ruff check src/`
- [ ] No type errors: `uv run mypy src/`
- [ ] Manual test successful: [specific curl/command]
- [ ] Error cases handled gracefully
- [ ] Logs are informative but not verbose
- [ ] Documentation updated if needed

---

## Anti-Patterns to Avoid
- ❌ Don't create new patterns when existing ones work
- ❌ Don't skip validation because "it should work"  
- ❌ Don't ignore failing tests - fix them
- ❌ Don't use sync functions in async context
- ❌ Don't hardcode values that should be config
- ❌ Don't catch all exceptions - be specific