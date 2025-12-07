FROM node:25-bookworm-slim

ARG TZ
ARG CLAUDE_CODE_VERSION=latest
ARG GIT_DELTA_VERSION=0.18.2
ARG ZSH_IN_DOCKER_VERSION=1.2.0
ARG USERNAME=node

ENV TZ="$TZ" \
    DEVCONTAINER=true \
    NPM_CONFIG_PREFIX=/usr/local/share/npm-global \
    SHELL=/bin/zsh \
    EDITOR=nano \
    VISUAL=nano

# Install system packages, update certificates, install GitHub CLI, and setup directories
RUN apt-get update && apt-get install -y --no-install-recommends \
  less git procps sudo fzf zsh man-db unzip gnupg2 curl ca-certificates \
  iptables ipset iproute2 dnsutils aggregate jq nano vim \
  && apt-get clean && rm -rf /var/lib/apt/lists/* \
  && update-ca-certificates \
  && curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg \
  && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && apt-get update \
  && apt-get install -y gh \
  && apt-get clean && rm -rf /var/lib/apt/lists/* \
  && mkdir -p /usr/local/share/npm-global /commandhistory /workspace /home/node/.claude \
  && touch /commandhistory/.bash_history \
  && chown -R node:node /usr/local/share /commandhistory /workspace /home/node/.claude \
  && ARCH=$(dpkg --print-architecture) \
  && curl -fsSL -o "git-delta_${GIT_DELTA_VERSION}_${ARCH}.deb" "https://github.com/dandavison/delta/releases/download/${GIT_DELTA_VERSION}/git-delta_${GIT_DELTA_VERSION}_${ARCH}.deb" \
  && dpkg -i "git-delta_${GIT_DELTA_VERSION}_${ARCH}.deb" \
  && rm "git-delta_${GIT_DELTA_VERSION}_${ARCH}.deb" \
  && mkdir -p /usr/share/doc/fzf/examples \
  && curl -fsSL -o /usr/share/doc/fzf/examples/key-bindings.zsh https://raw.githubusercontent.com/junegunn/fzf/master/shell/key-bindings.zsh \
  && curl -fsSL -o /usr/share/doc/fzf/examples/completion.zsh https://raw.githubusercontent.com/junegunn/fzf/master/shell/completion.zsh

WORKDIR /workspace

ENV PATH=/usr/local/share/npm-global/bin:/workspace/node_modules/.bin:$PATH

# Switch to node user for user-level installations
USER node

# Install zsh theme and Claude CLI globally
RUN sh -c "$(curl -fsSL https://github.com/deluan/zsh-in-docker/releases/download/v${ZSH_IN_DOCKER_VERSION}/zsh-in-docker.sh)" -- \
  -p git \
  -p fzf \
  -a "export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
  -x \
  && npm install -g @anthropic-ai/claude-code@${CLAUDE_CODE_VERSION}

# Copy and set up firewall script
COPY init-firewall.sh /usr/local/bin/
USER root
RUN chmod +x /usr/local/bin/init-firewall.sh \
  && echo "node ALL=(root) NOPASSWD: /usr/local/bin/init-firewall.sh" > /etc/sudoers.d/node-firewall \
  && echo "node ALL=(root) NOPASSWD: /bin/chown" >> /etc/sudoers.d/node-firewall \
  && chmod 0440 /etc/sudoers.d/node-firewall

USER node