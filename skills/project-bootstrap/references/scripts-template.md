# 版本管理脚本模板

以下脚本文件需要原样生成到目标项目中。

---

## VERSION 文件

文件路径：`VERSION`

```
0.1.0
```

---

## scripts/bump-patch.sh

```bash
#!/usr/bin/env bash
# 自动递增 patch 版本号（由 git hook 调用）
set -e

VERSION_FILE="$(git rev-parse --show-toplevel)/VERSION"
if [ ! -f "$VERSION_FILE" ]; then
  echo "0.1.0" > "$VERSION_FILE"
fi

VERSION=$(cat "$VERSION_FILE")
MAJOR=$(echo "$VERSION" | cut -d. -f1)
MINOR=$(echo "$VERSION" | cut -d. -f2)
PATCH=$(echo "$VERSION" | cut -d. -f3)

NEW_PATCH=$((PATCH + 1))
NEW_VERSION="$MAJOR.$MINOR.$NEW_PATCH"

echo "$NEW_VERSION" > "$VERSION_FILE"
git add "$VERSION_FILE"

echo "Version bumped: $VERSION -> $NEW_VERSION"
```

---

## scripts/bump-minor.sh

```bash
#!/usr/bin/env bash
# 递增 minor 版本号，重置 patch 为 0（人工调用）
set -e

VERSION_FILE="$(git rev-parse --show-toplevel)/VERSION"
VERSION=$(cat "$VERSION_FILE")
MAJOR=$(echo "$VERSION" | cut -d. -f1)
MINOR=$(echo "$VERSION" | cut -d. -f2)

NEW_MINOR=$((MINOR + 1))
NEW_VERSION="$MAJOR.$NEW_MINOR.0"

echo "$NEW_VERSION" > "$VERSION_FILE"
git add "$VERSION_FILE"
git commit -m "chore: bump version to $NEW_VERSION"

echo "Version bumped: $VERSION -> $NEW_VERSION"
```

---

## scripts/bump-major.sh

```bash
#!/usr/bin/env bash
# 递增 major 版本号，重置 minor 和 patch 为 0（人工调用）
set -e

VERSION_FILE="$(git rev-parse --show-toplevel)/VERSION"
VERSION=$(cat "$VERSION_FILE")
MAJOR=$(echo "$VERSION" | cut -d. -f1)

NEW_MAJOR=$((MAJOR + 1))
NEW_VERSION="$NEW_MAJOR.0.0"

echo "$NEW_VERSION" > "$VERSION_FILE"
git add "$VERSION_FILE"
git commit -m "chore: bump version to $NEW_VERSION"

echo "Version bumped: $VERSION -> $NEW_VERSION"
```

---

## .githooks/pre-commit

```bash
#!/usr/bin/env bash
# Git pre-commit hook: 自动递增 patch 版本号
# 仅在有实际代码变更（非仅 VERSION 文件变更）时触发

STAGED_FILES=$(git diff --cached --name-only)

# 如果只有 VERSION 文件变更，跳过（避免无限循环）
NON_VERSION_FILES=$(echo "$STAGED_FILES" | grep -v "^VERSION$" || true)
if [ -z "$NON_VERSION_FILES" ]; then
  exit 0
fi

# 递增 patch 版本
SCRIPT_DIR="$(git rev-parse --show-toplevel)/scripts"
if [ -f "$SCRIPT_DIR/bump-patch.sh" ]; then
  bash "$SCRIPT_DIR/bump-patch.sh"
fi
```

---

## 安装步骤

生成上述文件后，执行：

```bash
chmod +x .githooks/pre-commit scripts/bump-*.sh
git config core.hooksPath .githooks
```
