FROM ubuntu:25.10

RUN apt-get update && apt-get install -y \
    git \
    nodejs \
    npm
RUN npm install -g @anthropic-ai/claude-code \
    npm install -g @google/gemini-cli
RUN apt-get install -y \
    vim

COPY .vimrc /root/.vimrc
COPY .gitconfig /root/.gitconfig

RUN echo 'export PS1="dev:\$PWD# "' >> /root/.bashrc

ENV CLAUDE_CONFIG_DIR=/root/.claude

WORKDIR /dev-container-workdir

ENTRYPOINT ["/bin/bash"]

# docker build example:
# docker build -t dev-container -m 2GB .

# docker run example:
#  - claude 인증 정보를 저장해놓고 계속 다시 인증하지 않게 하기 위해서 .claude 디렉토리는 volume 에 저장한다
# docker run --rm -it -v ~/git/dev-container:/dev-container-workdir -v dev-container-volume-gemini:/root/.gemini -v dev-container-volume-claude:/root/.claude -e GEMINI_API_KEY=${GEMINI_API_KEY} dev-container
