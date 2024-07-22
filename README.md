# Reinforcement Learning baselines on the Real Robot

Under construction.

## Usage

Clone the repository locally and fetch all submodules with the following
command

    git clone git@github.com:kamiradi/real-rl.git
    git submodule update --init --recursive

From the parent directory run the following commands to fire up a docker
container and shell into it

    docker compose build
    docker compose up

In a different terminal you can retrieve shell access with the following.

    docker compose exec -it <name of service> bash
