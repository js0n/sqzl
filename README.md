# sqzl: A Lightweight Serial Command Runner

sqzl is a simple CLI tool for running commands sequentially with zero dependencies. It uses pipes to pass output between commands and stores command output in text files for easy auditing and debugging. Efficient and reliable, sqzl streamlines your workflows without the bloat.

## Create

This script is used to create new numbered Bash scripts that can be easily integrated into a pipeline managed by `sqzl run`. After creating scripts with `sqzl create`, you can use `sqzl bump` to adjust their numerical prefixes, allowing you to reorder or insert new scripts into your existing workflow.

For example, you might create a series of scripts:

1. `./sqzl create process-data`
2. `./sqzl create analyze-results`
3. `./sqzl create generate-report`

This would create scripts like `001-process-data.sh`, `002-analyze-results.sh`, and `003-generate-report.sh`.

If you later decide to add a new step between processing and analyzing, you could:

1. `./sqzl create clean-data`
2. `./sqzl bump -u 002-analyze-results.sh`

This would create `004-clean-data.sh` and bump up the numbers of the subsequent scripts.

Finally, you can execute your pipeline of scripts using `sqzl run`, which will run them in numerical order, passing the output of each script as input to the next.

```
Usage: ./sqzl create [SCRIPT_NAME]

Create a new Bash script with a numbered prefix.

Options:
  SCRIPT_NAME  Name for the new script (default: hello-world)

The script will be created with a numbered prefix (e.g., 001-script-name.sh)
and basic 'Hello, world!' content. The file will be made executable.

```

## Bump

The `sqzl bump` script is a powerful tool used in conjunction with `sqzl create` and `sqzl run` to manage and reorder your pipeline of numbered Bash scripts. Here's how it fits into the workflow:

1. After creating scripts with `sqzl create`, you might need to reorder them or insert new scripts between existing ones.
2. `sqzl bump` allows you to adjust the numerical prefixes of your scripts, effectively changing their execution order in the pipeline.
3. Once you've organized your scripts using `sqzl bump`, you can then use `sqzl run` to execute the entire pipeline in the correct order.

For example, if you've created scripts `001-input.sh`, `002-process.sh`, and `003-output.sh` using `sqzl create`, and you want to add a new script between processing and output:

1. Create the new script: `./sqzl create new-step`
2. Use bump to make room for the new script and renumber subsequent scripts: `./sqzl bump -u 003-output.sh`

This will result in the following pipeline:

- `001-input.sh`
- `002-process.sh`
- `003-new-step.sh`
- `004-output.sh`

Now your pipeline is ready to be run with `./sqzl run`.

This combination of tools provides a flexible and maintainable way to manage complex, multi-step data processing pipelines, with `sqzl bump` handling all necessary renumbering automatically.

```
Usage: ./sqzl bump [-n] [-d [step] | -u [step]] [<target_file>]

This script bumps numerical prefixes of files, starting from the specified target file
or the first file in numerical order if no target is specified.
It creates backups before renaming and deletes them upon successful completion.

Arguments:
  <target_file>  The file from which to start bumping numerical prefixes (optional).
                 If omitted, the first file in numerical order will be used.

Options:
  -n             Dry run: show what would be done without making changes
  -d [step]      Bump down: decrease numerical prefixes by the specified step (default: 1)
  -u [step]      Bump up: increase numerical prefixes by the specified step (default: 1)

Without -n, the script will perform actual file renaming.
With -n, it will only show the proposed changes without renaming files.

Output format (with -n):
  <original_filename> <new_filename>

Example:
  ./sqzl bump -n                 # Dry run, bump up by 1, starting from the first file
  ./sqzl bump -d                 # Bump down by 1, starting from the first file
  ./sqzl bump -d 2 002-file.txt   # Bump down by 2, starting from 002-file.txt
  ./sqzl bump -u 3               # Bump up by 3, starting from the first file
  ./sqzl bump 002-file.txt        # Bump up by 1 and perform actual renaming, starting from 002-file.txt

```

## Execute

The `sqzl run` script is designed to work seamlessly with the scripts created by `sqzl create` and managed by `sqzl bump`. Here's how these tools work together:

1. Use `sqzl create` to generate numbered script files for each step in your pipeline (e.g., `001-input.sh`, `002-process.sh`, `003-output.sh`).
2. If you need to add, remove, or reorder steps, use `sqzl bump` to adjust the numerical prefixes of your scripts.
3. Finally, execute your entire pipeline with `sqzl run`, which will run the scripts in numerical order, passing the output of each step as input to the next.

This workflow allows you to easily maintain and modify complex data processing pipelines. You can insert new steps, remove existing ones, or change the order of operations by using `sqzl create` to add new scripts and `sqzl bump` to renumber them as needed. Then, `sqzl run` takes care of executing the pipeline in the correct order, handling input and output between steps automatically.

```
Usage: ./sqzl run [-i input_file] [-h] [--help]
Run a series of numbered scripts in order, passing output between them.

Options:
  -i input_file    Specify the input file (default: input.txt)
  -h, --help       Display this help message and exit

```
