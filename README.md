# RoseeNFT
just tesing gitub.
#!/usr/bin/env python3
# -*- coding: utf-8 -*-8

"""
generate_readme.py
A tiny README generator/updater for any repository.

- Scans the repo (excluding common dirs like .git, venv, node_modules, __pycache__).
- Builds a compact directory tree.
- Counts files/lines and estimates languages by file extension.
- Creates README.md if missing OR updates only the section between
  <!-- AUTO-README:START --> and <!-- AUTO-README:END --> to be CI-friendly.

Usage:
    python generate_readme.
Optional:
    python generate_readme.py --max-depth 3 --ignore "data,build,dist"
"""

from __future__ import annotations
import os
import sys
import argparse
from collections import Counter, defaultdict
from pathlib import Path
from typing import Iterable

AUTO_START = "<!-- AUTO-README:START -->"
AUTO_END = "<!-- AUTO-README:END -->"

DEFAULT_IGNORES = {
    ".git", ".github", "__pycache__", "venv", ".venv",
    "node_modules", "dist", "build", ".idea", ".vscode",
    ".mypy_cache", ".pytest_cache", ".DS_Store", ".next",
    ".pnpm-store", ".cache"
}

EXT_LANG = {
    ".py": "Python", ".js": "JavaScript", ".ts": "TypeScript", ".tsx": "TypeScript",
    ".jsx": "JavaScript", ".rb": "Ruby", ".go": "Go", ".rs": "Rust", ".java": "Java",
    ".kt": "Kotlin", ".c": "C", ".h": "C/C++ Header", ".hpp": "C++", ".cpp": "C++",
    ".cs": "C#", ".php": "PHP", ".swift": "Swift", ".m": "Objective-C",
    ".sh": "Shell", ".zsh": "Shell", ".ps1": "PowerShell", ".sql": "SQL",
    ".html": "HTML", ".css": "CSS", ".scss": "SCSS", ".md": "Markdown",
    ".yml": "YAML", ".yaml": "YAML", ".toml": "TOML", ".json": "JSON",
    ".R": "R", ".scala": "Scala", ".lua": "Lua", ".dart": "Dart"
}

TEXT_EXTS = set(EXT_LANG.keys()) | {
    ".txt", ".ini", ".cfg", ".env", ".gitignore", ".gitattributes", ".editorconfig"
}

def human_int(n: int) -> str:
    return f"{n:,}".replace(",", "٬")  # Persian thousand separator look

def is_text_file(path: Path) -> bool:
    return path.suffix in TEXT_EXTS

def count_file_lines(path: Path) -> int:
    try:
        with path.open("r", encoding="utf-8", errors="ignore") as f:
            return sum(1 for _ in f)
    except Exception:
        return 0

def walk_repo(root: Path, ignore: set[str], max_depth: int) -> Iterable[Path]:
    root_depth = len(root.parts)
    for dirpath, dirnames, filenames in os.walk(root):
        p = Path(dirpath)
        depth = len(p.parts) - root_depth
        # filter ignores
        dirnames[:] = [d for d in dirnames if d not in ignore]
        if max_depth >= 0 and depth > max_depth:
            dirnames[:] = []
            continue
        for fn in filenames:
            yield p / fn

def build_tree(root: Path, ignore: set[str], max_depth: int) -> str:
    def tree(dir_path: Path, prefix: str = "", depth: int = 0) -> list[str]:
        if max_depth >= 0 and depth > max_depth:
            return []
        entries = []
        try:
            entries = [e for e in dir_path.iterdir() if e.name not in ignore]
        except PermissionError:
            return [prefix + "└── [permission denied]"]
        entries.sort(key=lambda e: (e.is_file(), e.name.lower()))
        lines = []
        for i, e in enumerate(entries):
            connector = "└── " if i == len(entries) - 1 else "├── "
            if e.is_dir():
                lines.append(prefix + connector + e.name + "/")
                extension = "    " if i == len(entries) - 1 else "│   "
                lines += tree(e, prefix + extension, depth + 1)
            else:
                lines.append(prefix + connector + e.name)
        return lines

    lines = [root.name + "/"] + tree(root, depth=1)
    return "```\n" + "\n".join(lines) + "\n```"

def summarize(files: Iterable[Path]) -> tuple[int, int, dict[str, int]]:
    files = list(files)
    total_files = len(files)
    total_lines = 0
    lang_counter: Counter[str] = Counter()
    for p in files:
        if p.is_file():
            if is_text_file(p):
                total_lines += count_file_lines(p)
            lang = EXT_LANG.get(p.suffix)
            if lang:
                lang_counter[lang] += 1
    return total_files, total_lines, dict(lang_counter.most_common(8))

def make_auto_section(root: Path, ignore: set[str], max_depth: int) -> str:
    files = [p for p in walk_repo(root, ignore, max_depth=9999) if p.is_file()]
    total_files, total_lines, langs = summarize(files)
    tree_md = build_tree(root, ignore, max_depth)
    langs_md = ", ".join(f"{k} ({human_int(v)})" for k, v in langs.items()) or "—"

    return f"""## نمای کلی ریپو
.
"""

def update_or_create_readme(root: Path, content: str) -> None:
    readme = root / "README.md"
    auto_block = f"{AUTO_START}\n{content}\n{AUTO_END}\n"
    if readme.exists():
        text = readme.read_text(encoding="utf-8", errors="ignore")
        if AUTO_START in text and AUTO_END in text:
            before = text.split(AUTO_START)[0]
            after = text.split(AUTO_END)[-1]
            new_text = before + auto_block + after
        else:
            # Append an auto section to existing README
            new_text = text.rstrip() + "\n\n" + auto_blockname: Auto README
on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import os
from pathlib import Path

AUTO_START = "<!-- AUTO-README:START -->"
AUTO_END   = "<!-- AUTO-README:END -->"

IGNORES = {".git", ".github", "__pycache__", "node_modules", "venv", ".venv"}

def tree(dir_path: Path, prefix: str = "", depth: int = 0, max_depth: int = 2):
    if depth > max_depth:
        return []
    lines = []
    entries = [e for e in dir_path.iterdir() if e.name not in IGNORES]
    entries.sort(key=lambda e: (e.is_file(), e.name.lower()))
    for i, e in enumerate(entries):
        connector = "└── " if i == len(entries) - 1 else "├── "
        if e.is_dir():
            lines.append(prefix + connector + e.name + "/")
            extension = "    " if i == len(entries) - 1 else "│   "
            lines += tree(e, prefix + extension, depth + 1, max_depth)
        else:
            lines.append(prefix + connector + e.name)
    return lines

def generate_section(root: Path, max_depth: int = 2) -> str:
    lines = [root.name + "/"] + tree(root, max_depth=max_depth)
    return "## ساختار ریپو\n\n```\n" + "\n".join(lines) + "\n```\n"

def update_readme(root: Path, section: str):
    readme = root / "README.md"
    block = f"{AUTO_START}\n{section}{AUTO_END}\n"
    if readme.exists():
        text = readme.read_text(encoding="utf-8")
        if AUTO_START in text and AUTO_END in text:
            before = text.split(AUTO_START)[0]
            after = text.split(AUTO_END)[-1]
            new_text = before + block + after
        else:
            new_text = text.rstrip() + "\n\n" + block
    else:
        new_text = f"# {root.name}\n\n{block}"
    readme.write_text(new_text, encoding="utf-8")

if __name__ == "__main__":
    root = Path(".").resolve()
    section = generate_section(root, max_depth=2)
    update_readme(root, section)
    print("✅ README.md به‌روزرسانی شد.")

      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      - run: python generate_readme.py --max-depth 3
      - name: Commit changes
        run: |
          if [[ -n "$(git status --porcelain)" ]]; then
            git config user.name "github-actions[bot]"
            git config user.email "github-actions[bot]@users.noreply.github.com"
            git add README.md
            git commit -m "docs: update auto README"
            git push
          else
            echo "No changes to commit."
          fi

        readme.write_text(new_text, encoding="utf-8")
    e#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
generate_changelog.py
Creates/updates CHANGELOG.md from git history.

- Groups commits by type (feat, fix, docs, refactor, perf, test, build, ci, chore, deps, other)
- Uses latest tag (git tag) as the previous release boundary; if no tag exists, uses the first commit.
- Supports PR numbers (#123) and commit short hashes.
- Updates only the section between <!-- AUTO-CHANGELOG:START --> and <!-- AUTO-CHANGELOG:END -->.

Usage:
    python generate_changelog.py
Optional:
    python generate_changelog.py --since v1.2.0  # override range start
    python generate_changelog.py --max-commits 500
"""

from __future__ import annotations
import argparse
import datetime as dt
import os
import re
import subprocess
from pathlib import Path
from typing import List, Tuple, Dict

AUTO_START = "<!-- AUTO-CHANGELOG:START -->"
AUTO_END   = "<!-- AUTO-CHANGELOG:END -->"

TYPES = [
    ("feat", "Features"),
    ("fix", "Bug Fixes"),
    ("docs", "Documentation"),
    ("refactor", "Refactoring"),
    ("perf", "Performance"),
    ("test", "Tests"),
    ("build", "Build System"),
    ("ci", "CI"),
    ("chore", "Chores"),
    ("deps", "Dependencies"),
]

COMMIT_PATTERN = re.compile(
    r"^(?P<type>feat|fix|docs|refactor|perf|test|build|ci|chore|deps)"
    r"(?:\((?P<scope>[^)]+)\))?!?:\s*(?P<desc>.+)$",
    re.IGNORECASE
)

def sh(cmd: List[str]) -> str:
    return subprocess.check_output(cmd, text=True).strip()

def latest_tag() -> str | None:
    try:
        return sh(["git", "describe", "--tags", "--abbrev=0"])
    except subprocess.CalledProcessError:
        return None

def list_commits(since: str | None, max_commits: int) -> List[Tuple[str, str]]:
    range_spec = f"{since}..HEAD" if since else None
    fmt = "%h%x1f%s"
    cmd = ["git", "log", f"--max-count={max_commits}", f"--format={fmt}"]
    if range_spec:
        cmd.insert(2, range_spec)
    out = sh(cmd)
    commits = []
    for line in out.splitlines():
        if not line.strip():
            continue
        try:
            h, s = line.split("\x1f", 1)
        except ValueError:
            continue
        commits.append((h, s))
    commits.reverse()  # oldest -> newest (optional)
    return commits

def parse_commit(msg: str) -> Tuple[str, str, str]:
    """
    Returns (type, scope, desc). If not matched, type='other', scope='', desc=msg
    """
    m = COMMIT_PATTERN.match(msg.strip())
    if not m:
        return ("other", "", msg.strip())
    t = m.group("type").lower()
    scope = m.group("scope") or ""
    desc = m.group("desc").strip()
    return (t, scope, desc)

def group_commits(commits: List[Tuple[str, str]]) -> Dict[str, List[Tuple[str, str, str, str]]]:
    groups: Dict[str, List[Tuple[str, str, str, str]]] = {t[0]: [] for t in TYPES}
    groups["other"] = []
    for short, msg in commits:
        t, scope, desc = parse_commit(msg)
        groups.setdefault(t, []).append((short, t, scope, desc))
    return groups

def render_group(title: str, items: List[Tuple[str, str, str, str]]) -> str:
    if not items:
        return ""
    lines = [f"### {title}"]
    for short, t, scope, desc in items:
        # Try to keep PR reference if present (#123)
        pr = ""
        m = re.search(r"(#\d+)", desc)
        if m:
            pr = f" {m.group(1)}"
        scope_txt = f"**{scope}**: " if scope else ""
        lines.append(f"- {scope_txt}{desc} ({short}){pr}")
    return "\n".join(lines) + "\n"

def build_section(since: str | None, max_commits: int) -> str:
    today = dt.date.today().isoformat()
    commits = list_commits(since, max_commits)
    grouped = group_commits(commits)

    header = f"## Changes ({today})\n\n"
    body_parts = []
    for key, title in TYPES:
        body_parts.append(render_group(title, grouped.get(key, [])))
    body_parts.append(render_group("Other", grouped.get("other", [])))
    body = "".join(bp for bp in body_parts if bp)

    if not body.strip():
        body = "_No changes found in the selected range._\n"

    return header + body

def update_changelog(root: Path, section_md: str) -> None:
    changelog = root / "CHANGELOG.md"
    block = f"{AUTO_START}\n{section_md}{AUTO_END}\n"
    if changelog.exists():
        text = changelog.read_text(encoding="utf-8", errors="ignore")
        if AUTO_START in text and AUTO_END in text:
            before = text.split(AUTO_START)[0]
            after = text.split(AUTO_END)[-1]
            new_text = before + block + after
        else:
            new_text = text.rstrip() + "\n\n" + block
    else:
        title = "# Changelog\n\n"
        intro = "این فایل به صورت خودکار از تاریخچه‌ی گیت ساخته/به‌روزرسانی می‌شود.\n\n"
        new_text = title + intro + block
    changelog.write_text(new_text, encoding="utf-8")

def main():
    ap = argparse.ArgumentParser()
    ap.add_argument("--since", help="شروع رنج لاگ (مثلاً آخرین تگ v1.2.0). اگر خالی باشد از آخرین تگ استفاده می‌شود.", default=None)
    ap.add_argument("--max-commits", type=int, default=2000, help="حداکثر تعداد کمیت‌ها برای پردازش")
    args = ap.parse_args()

    root = Path(".").resolve()
    start = args.since or latest_tag()
    section = build_section(start, args.max_commits)
    update_changelog(root, section)
    print("✅ CHANGELOG.md ساخته/به‌روزرسانی شد.")
    if start:
        print(f"   محدودهٔ تغییرات: {start}..HEAD")
    else:
        print("   محدودهٔ تغییرات: از اولین کمیت تا HEAD (چون تگی پیدا نشد).")

if __name__ == "__main__":
    main()

