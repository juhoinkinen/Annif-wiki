The Annif REST API and CLI include functionalities to detect the language of text given some candidate languages. 
Annif projects are typically language specific, so a text of a given language needs to be processed with a project intended for that language. Annif's in-built language detection functionality can help in this. 

The language detection feature utilizes the [Simplemma](https://github.com/adbar/simplemma) library, and the candidate languages must be selected from [those supported](https://github.com/adbar/simplemma?tab=readme-ov-file#supported-languages) by Simplemma.

# REST API
The `/v1/detect-language` endpoint accepts POST requests with a JSON object containing the text to be analyzed and a list of candidate languages given in [IETF BCP 47 codes](https://en.wikipedia.org/wiki/IETF_language_tag), for example:
```
{
  "languages": ["en", "fi"],
  "text": "A quick brown fox jumped over the lazy dog."
}
```
Note: The number of candidate languages is limited to 5.

The response is a JSON object that provides probability scores for each candidate language:
```
{
  "results": [
    {"language": "en", "score": 0.85},
    {"language": "fi", "score": 0.1},
    {"language": null, "score": 0.1} 
  ]
}
```
The scores range from 0 to 1 and a null value is used for an unknown language.

# CLI
To detect the language using the CLI, use the `detect-language` command. Provide the candidate language codes as a comma-separated argument, and the text to analyze via standard input. For example:
```
$ echo "this is example text" | annif detect-language fi,sv,en
en	1.0000
sv	0.3333
fi	0.0000
?	0.0000
```
You can also analyze multiple files by specifying their paths, similarly as in the `annif suggest` command. For example:
```
$ annif detect-language fi,sv,en README.md LICENSE.txt 
Detected languages for README.md
en	0.7700
sv	0.2100
?	0.2000
fi	0.1400
Detected languages for LICENSE.txt
en	0.8857
sv	0.3714
fi	0.0857
?	0.0571
```

---
[[← Transforms|Transforms]] | [[🤗 Hugging Face Hub integration →|Hugging-Face-Hub-integration]]