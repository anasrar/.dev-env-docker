FROM alpine AS base
WORKDIR /root

# # update repo packages
RUN echo -e "http://dl-cdn.alpinelinux.org/alpine/edge/main\nhttp://dl-cdn.alpinelinux.org/alpine/edge/community" > /etc/apk/repositories

# # download language
RUN apk add --no-cache build-base cmake clang clang-extra-tools

FROM base AS dev
# # development env
# ## utilities
RUN apk add --no-cache bash curl git

# ## install tmux, lazygit, neovim, tree-sitter, fd, ripgrep, and stow
RUN apk add --no-cache tmux lazygit neovim tree-sitter tree-sitter-cli fd ripgrep stow

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

# ## change shell to bash for tmux
RUN echo "set -g default-shell \"/bin/bash\"" >> ~/.tmux.conf
RUN echo "set -g default-command \"/bin/bash\"" >> ~/.tmux.conf

# ## login bash
CMD ["bash"]
