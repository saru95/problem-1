# problem-1

## Install

The shell commands mentioned below install `pipenv`, clone the repository, and setup a Python 3.7 virtual environment along with installing all the Python packages.

```sh
pip install pipenv
git clone https://github.com/saru95/problem-1; cd problem-1/
pipenv install; pipenv shell
```

## Execution

The mining script is present within the folder `argument_commits/`. To use it as a module, follow `test.py` and just run,

```sh
python test.py # or your_file_name.py
```

```py
from argument_commits import ArgumentCommits

# The constructor takes in a list of git URLs.
c = ArgumentCommits(urls=['https://github.com/google/guava', ....])

# This routine finds the commits where a function argument was added to a Java file.
c.find_commits_with_additional_parameters()

# This routine writes the result of the routine above to a CSV file. It takes in the name of the file as the argument.
c.write_to_csv(filename='')

# This routine writes the result of the routine above to a TSV file. It takes in the name of the file as the argument.
c.write_to_tsv(filename='')
```

## Process

1. The script first fetches all the commit data and runs static analysis on the code present within the commit to identify the functions and their signatures. Using this, we maintain a record in the following manner,

```py
{
    'filename~functionname': [
        {
            'hash': '...',
            'doc': '...',
            'current_signature': '...',
            'args': []
        },
        {
            ...
        }
    ],
    ...
}
```

This represents all the states of a particular function during a particular commit.

2. After we have this record, we sort the commits for each function within a file by it's `doc` (date of commit) and then find out the commits where the number of arguments were more than the previous commit and where the `current_signature` differs.

## Corner Cases

* When the same function is overloaded multiple times within a file, it is sometimes considered as addition of an argument. This is handled by comparing the hashes of the changes.
* Lizard, which performs the static analysis of the files, considers `for()` as a function. This case has also been handled.

## Samples

As a test, the script was run on 3 famous repositories:

1. RxJava:
    * Total Number of Commits: ~5471
    * Time taken: ~16 mins.
    * Link: https://github.com/ReactiveX/RxJava
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/RxJava.csv">Generated CSV</a>
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/RxJava.tsv">Generated TSV</a>

2. ZXing:
    * Total Number of Commits: ~3475
    * Time taken: ~3 mins.
    * Link: https://github.com/zxing/zxing
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/ZXing.csv">Generated CSV</a>
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/ZXing.tsv">Generated TSV</a>

2. jadx:
    * Total Number of Commits: ~745
    * Time taken: ~1 min.
    * Link: https://github.com/skylot/jadx
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/jadx.csv">Generated CSV</a>
    * <a href="https://github.com/saru95/problem-1/blob/master/Samples/jadx.tsv">Generated TSV</a>


## Issues

CSVs are bad at handling values with commas within them (<a href="https://stackoverflow.com/a/4618007/3301488">src</a>). To mitigate this, the script also supports a function called `write_to_tsv` which writes to a TSV file instead.
