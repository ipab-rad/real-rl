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

    docker compose exec -it <name of service: (real_rl)> bash

For now, every time you start the container run the following (TODO: add the
following to the Dockerfile)

    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
    echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
    echo 'eval "$(pyenv init -)"' >> ~/.bashrc
    exec "$SHELL"
    pyenv shell 3.10.4
    cd /root/workspace/src/reasoning/serl/serl_launcher
    python -m pip install --upgrade pip
    pip install -e .
    pip install -r requirements.txt

## Notes
`serl_robot_infra` - is responsible for spawning all the real robot related
software. It requires the default python version coming with Ubuntu 20 - 
python 3.8.10. This ultimately starts a HTTPs server that is used to
communicate to the robot

`serl_launcher` - is responsible for spawning all the RL and MDP reasoning
related scripts. It acts as the HTTPs client that attaches to the robot HTTPs
server. Here you use python 3.10

# Troubleshooting and optimization

## 3.8 -> 3.10

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
