# LLMan

Fine-tuning T5 for generating Linux man page sections.

## T5

T5 (Text-To-Text Transfer Transformer) is a transformer designed for sequence-to-sequence tasks The encoder-decoder architecture is well-suited for generating structured documentation like man pages, where one section (input) is used to predict remaining sections (output).

## Data

Scraped man pages from Linux system in JSON.

- **man_pages.json** - Contains the complete man page with all sections
- **masked_man_pages.json** - Contains input/output pairs ready for fine-tuning. The intention is to input a section (such as "gargoyle - curse your friends with dark magic") and receive an output of the remaining sections of the man page.

## Training Approach

The model is trained to predict remaining sections of a man page given one section as input. For each man page, one section is used as input and all other sections become the output.

**Example training sample:**

Input:
```
<SECTION>NAME</SECTION>
gargoyle - curse your friends with dark magic
```

Output:
```
<SECTION>SYNOPSIS</SECTION>
gargoyle [options]

<SECTION>DESCRIPTION</SECTION>
Gargoyle is a curse utility that can be used to curse your friends with dark magic...

<SECTION>OPTIONS</SECTION>
-h, --help Show help options...
```

## Files

- **scrape.py** - Extracts man pages from local `/usr/share/man/` directory, processes groff files, and saves to JSON
- **gen_samples.py** - Generates fine-tuning samples by creating input/output pairs from man page sections
- **t5-base-finetune.ipynb** - Fine-tunes T5-large on man pages using LoRA and 4-bit quantization

## Usage

1. Extract man pages: `python scrape.py`
2. Generate training samples: `python gen_samples.py`
3. Fine-tune model: Run `t5-base-finetune.ipynb`

## Example Output

After fine-tuning, given input:
```
gargoyle - curse your friends with dark magic
```

The model generates:
```
NAME

gargoyle - curse your friends with dark magic

SYNOPSIS

gargoyle [options]

DESCRIPTION

Gargoyle is a curse utility that can be used to curse your friends with dark magic. The program can also be used as a backup for your old favorites. This program allows you to use the curse utility to create and destroy their enemies. It is not recommended to run gargoyle on your own machine, but it might help you in creating your own. When you do this, you can get some help from the Gargoyle documentation.

OPTIONS

-h, --help Show help options. -V, --version Show version information. -v, --verbose Print out a usage message. -l, --version Print version information and exit. -h, --help Display help messages and exit. -n, --verbose Print out a usage message. -u, --language Print version information and exit. -c, --guid[=false] Print out a short usage message. -i, --indicate Use only a generic GUI. -p, --help Print a brief usage message. -m, --help Print a summary of options. -t, --version Print version information.

SEE ALSO

gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1), gargoyle(1)
```

