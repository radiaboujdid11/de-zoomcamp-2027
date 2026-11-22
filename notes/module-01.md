# Module 1 - Docker basics

- I cloned the course repo to read it, and created my own repo to push my notes and exercises.
- A container is a disposable mini-environment. An image is the template it is created from.
- I ran Ubuntu with `docker run -it --rm ubuntu`. `--rm` deletes the container when I exit.
- I installed Python inside it, and it was gone in the next container: containers are stateless.
- I used `python:3.9.16-slim` and `--entrypoint=bash` to get a shell instead of Python.
- A volume shares a folder between my computer and the container. I created files on Windows and read them from inside the container.
- On Windows PowerShell: one-line commands, `${PWD}` instead of `$(pwd)`, and quotes around paths with spaces.
- I fixed a Docker clock error that blocked `apt update`.
## Commands I used

| Command | What it does |
|---|---|
| `docker run -it --rm ubuntu` | Starts an Ubuntu container with a terminal, deleted when I exit |
| `docker run -it --rm python:3.9.16-slim` | Starts a container with Python already installed |
| `--entrypoint=bash` | Opens a shell instead of the image's default program |
| `-v "${PWD}/test:/app/test"` | Shares my `test` folder with the container (volume) |
| `docker ps -a` | Lists all containers, including stopped ones |
| `docker rm $(docker ps -aq)` | Deletes all stopped containers |
| `docker images` | Lists the images downloaded on my computer |
| `apt update && apt install python3` | Installs Python inside the Ubuntu container |
| `exit` | Leaves the container (bash). In Python, it is `exit()` |
| `git add .` / `git commit -m "..."` / `git push` | Saves my work and sends it to GitHub |
## The `list_files.py` script

- It looks at the folder where it runs (`Path.cwd()`) and loops over everything inside it.
- It skips itself, using `Path(__file__).name`, so it only lists the other files.
- For each file, it prints the name and its content (`read_text`), so I can check what the container sees.
- I used it to prove that the container reads the files from my computer through the volume.
## Lesson 2 - Virtual environments and data pipelines

### What is a data pipeline?
- A pipeline takes data in, changes it, and gives data out.
- Example: a CSV file -> cleaned with pandas -> saved as a file or a database table.
- In my first pipeline, the input is just a number (the day), pandas builds a small table, and the output is a Parquet file.

### My script (`pipeline.py`)
- `sys.argv` holds the words I type after `python`. `sys.argv[1]` is the day, as text, and `int()` turns it into a number.
- A DataFrame is a table in memory, made with pandas. It is a working tool, the result is the saved file.
- `df.to_parquet(...)` saves the table as a Parquet file. Parquet is compact and fast, and it keeps column types.
- I run it with `uv run python pipeline.py 10`.

### Why a virtual environment?
- `pip install` installs for the whole computer, and projects can clash.
- A virtual environment keeps each project's libraries separate, like Docker does for a whole system.

### uv
- `uv init` creates the project (`pyproject.toml`, `.python-version`).
- `uv add pandas pyarrow` installs the libraries and records them in `pyproject.toml`.
- `uv run python ...` runs my code inside the project's environment.
- `--python=3.13` is my choice of version. uv downloads it if needed.
- My computer's Python (`py -V`, 3.14.7) and the project's (`uv run python -V`, 3.13) are different, which is the point.

### Git: `.gitignore`
- It lists files Git must ignore. I added `*.parquet`, because generated results should not go to GitHub.
- `git check-ignore -v file` checks that a file is ignored.

### Windows differences
- `which python` does not exist. I use `uv run python -c "import sys; print(sys.executable)"`.
- `uv init` fails if `pyproject.toml` already exists, which is fine.
- Python code goes in a `.py` file, not in the terminal.