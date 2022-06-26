FROM denoland/deno:alpine AS base
WORKDIR /root

# # update repo packages
RUN echo -e "http://dl-cdn.alpinelinux.org/alpine/edge/main\nhttp://dl-cdn.alpinelinux.org/alpine/edge/community" > /etc/apk/repositories

FROM base AS dev
# # development env
# ## utilities
RUN apk add --no-cache bash curl git

# ## install tmux, lazygit, neovim, tree-sitter, fd, ripgrep, stow, and packer
RUN apk add --no-cache tmux lazygit neovim tree-sitter tree-sitter-cli fd ripgrep stow
RUN git clone --depth 1 https://github.com/wbthomason/packer.nvim ~/.local/share/nvim/site/pack/packer/start/packer.nvim

# ## install dap
# you can debug deno with node js debug2 attach (see: `node/Dockerfile` and `.config/nvim/lua/DAP/typescript.lua`)
# ### debugging deno using chrome from docker container
# don't forget to bind port (for example `-p 9229:9229`) when run docker images, so the command something like this `deno run --inspect-brk=0.0.0.0:9229 main.ts`
# run chromium based browser and on address bar type `chrome://inspect`
# source: https://deno.land/manual@v1.22.3/getting_started/debugging_your_code
# ## clone dotfiles (bash, neovim, lazygit, and tmux)

# ## clone dotfiles (bash, neovim, lazygit, and tmux)
RUN git clone --depth 1 https://github.com/anasrar/.dotfiles.git ~/.dotfiles && cd ~/.dotfiles && ./stow.sh bash && ./stow.sh neovim && ./stow.sh lazygit && ./stow.sh tmux

# ## compile neovim plugin
RUN nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerSync'
RUN nvim --headless -c 'autocmd User PackerComplete quitall' -c 'PackerSync'

# ## tree-sitter parser
RUN wget https://github.com/anasrar/nvim-treesitter-parser-bin/releases/download/alpine/all.zip
RUN unzip -j all.zip *.so -d ~/.local/share/nvim/site/pack/packer/start/nvim-treesitter/parser/

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
