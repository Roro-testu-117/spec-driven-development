FROM python:3.12-slim

# システムパッケージのインストール
RUN apt-get update && apt-get install -y \
    build-essential \
    tzdata \
    vim \
    git \
    git-secrets \
    curl \
    ripgrep \
    && ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Rustコンパイラ(一部のPythonライブラリのビルドで必要)
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"

# uvのインストール
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# 環境変数設定
ENV TZ=Asia/Tokyo

# 作業ディレクトリ設定
WORKDIR /workspace

# Python依存関係のインストール
COPY pyproject.toml /workspace/
RUN uv pip install --system .

# Node.js 24 LTS のインストール
RUN curl -fsSL https://deb.nodesource.com/setup_24.x | bash - \
    && apt-get install -y nodejs \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Gemini CLI & Claude Codeのインストール
RUN npm install -g @google/gemini-cli @anthropic-ai/claude-code @openai/codex

