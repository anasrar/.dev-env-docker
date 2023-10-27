FROM alpine AS base
WORKDIR /root

# # update repo packages
RUN echo -e "http://dl-cdn.alpinelinux.org/alpine/edge/main\nhttp://dl-cdn.alpinelinux.org/alpine/edge/community" > /etc/apk/repositories

# # download language
RUN apk add --no-cache build-base curl
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
RUN echo "source ~/.cargo/env" >> ~/.bashrc

FROM base AS dev
# # development env
# ## utilities
RUN apk add --no-cache bash git

# ## install tmux, lazygit, neovim, tree-sitter, fd, ripgrep, and stow
RUN apk add --no-cache tmux lazygit neovim tree-sitter tree-sitter-cli fd ripgrep stow

# ## install lsp, linter, and dap
RUN source $HOME/.cargo/env; rustup component add rust-src rust-analyzer

# ## clone dotfiles (bash, neovim, lazygit, and tmux)
RUN git clone --depth 1 https://github.com/anasrar/.dotfiles.git ~/.dotfiles && cd ~/.dotfiles && ./stow.sh bash && ./stow.sh neovim && ./stow.sh lazygit && ./stow.sh tmux

# ## compile neovim plugin
RUN nvim --headless "+Lazy! sync" +qa

# ## tree-sitter parser
RUN wget https://github.com/anasrar/nvim-treesitter-parser-bin/releases/download/linux/all.zip
RUN unzip -j all.zip *.so -d ~/.local/share/nvim/lazy/nvim-treesitter/parser/
RUN rm all.zip

# ## tmux color and UTF8
ENV TERM xterm-256color
RUN echo "alias tmux='tmux -u'" >> ~/.bashrc

# ## bashrc
RUN echo "source ~/.bashrc_personal" >> ~/.bashrc

# ## change shell to bash
RUN echo "set -g default-shell \"/bin/bash\"" >> ~/.tmux.conf
RUN echo "set -g default-command \"/bin/bash\"" >> ~/.tmux.conf

# ## login bash
CMD ["bash"]
