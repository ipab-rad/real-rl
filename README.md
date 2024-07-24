# Reinforcement Learning baselines on the Real Robot

Under construction.

## Usage

Clone the repository locally and fetch all submodules with the following
command

    git clone git@github.com:ipab-rad/real-rl.git
    git submodule update --init --recursive

From the parent directory run the following commands to fire up a docker
container and shell into it

    docker compose build
    docker compose up

In a different terminal you can retrieve shell access with the following.

    docker compose exec -it <name of service> bash


# Troubleshooting and optimization

## 3.8 - 3.10

Pyenv steps

    git clone https://github.com/pyenv/pyenv.git ~/.pyenv
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
    echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(pyenv init -)"' >> ~/.bashrc
    exec "$SHELL"
    pyenv install 3.10.4
    pyenv shell 3.10.4
    cd <dir>/serl/serl_launcher
    pip install -e .
    pip install -r requirements.txt
