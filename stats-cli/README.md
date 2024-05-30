# stats.py

v1.1

stats.py is a powerful yet easy-to-use tool for analyzing model performance.
It quickly and accurately calculates model accuracy statistics for different arrhythmia classifications,
while also outputting which events were falsely classified. 

Made by the GFU 2023-2024 Senior Design Team

## Changes

- Updated to v1.1! Changes made from v1.0:
  - The tool now takes in two JSON files instead of one for decoupling predictions and actual classifications.
  - Passing lists of labels is now optional in the `actual` file.
  - No more parallel arrays!
  - The filenums array has been replaced by the actual filenames.
  - Predicted files now supports passing dictionaries of probabilities instead of just pure classifications.
  The `-c` flag (or `--confidence`) can be used to specify the minimum level of confidence for classifications.
  - The README has been updated to reflect these changes.

### Abbreviations Used

- Sn: Sensitivity
- PPV: Positive Predictive Value
- TP: True Positive
- TN: True Negative
- FP: False Positive
- FN: False Negative

## Requirements

Requires Python 3.10 or later, which can be downloaded from https://www.python.org/downloads/.

## Parameters

- `predicted`: The name of the JSON file holding the data of the predicted classifications. It must have a
dictionary of filenames mapped to either a single classification or a dictionary of classifications and their probabilities.
Note that the filenames used must be a subset of the data passed in the "actual" file.

- `actual`: The name of the JSON file holding the data of the actual classifications. It must have one or two entries:
  - `filenames`: A dictionary of filenames, with each mapped to a single classification.
  - `labels`: A list of classification types. Optional; if not passed, then the tool simply looks at all unique classifications.

Please check the provided example files for more information.

### Optional Parameters

- `-h, --help`: Shows the help display.

- `-a ARRHYTHMIA, --arrhythmia ARRHYTHMIA`: The arrhythmia to print FP and FN results for. Defaults to "Artifact."

- `-c CONFIDENCE, --confidence CONFIDENCE`:  Allows lists of probabilities to be passed into the predicted file instead
of pure classifications. Must be a value between 0 and 1.

- `--single`: Prints accuracy statistics for only the arrhythmia passed into `--arrhythmia`.

- `--print-none`: Prints no recording/event numbers of FP or FN results. Mutually exclusive with `--print-all`
and causes `--arrhythmia` to be silently ignored.

- `--print-all`: Prints the recording/event numbers of FP and FN classifications for all arrhythmias.
Mutually exclusive with `--print-none` and causes `--arrhythmia` to be silently ignored.

- `--precision PRECISION`: The precision (in digits) of the displayed values. Defaults to 3.

- `-f, --formatted`: Formats for nice output in standard out.

## Usage

Provided alongside the stats tool should be a whopping 10 example JSON files, grouped in 5 pairs.
The ten files are as such:
- 6 examples with pure classifications:
  - *simple_predicted.json* and *simple_actual.json*
  - *full_predicted.json* and *full_actual.json*
  - *numeric_predicted.json* and *numeric_actual.json*
- 4 examples with probability dictionaries in the predicted files:
  - *simple_prob_predicted.json* and *simple_prob_actual.json*
  - *full_prob_predicted.json* and *full_prob_actual.json*

Below, we'll use each to demonstrate how to use the tool.

### Precision and Formatting

To run the program on the command-line, simply pass the program name to the Python interpreter
along with the name of the JSON files to analyze. The tool takes in two files: the first is the file with
the predictions, while the second is the file with the actual classifications.

```bash
python stats.py simple_predicted.json simple_actual.json
```

Running this prints the results to standard out in a format that can easily be copy
and pasted to other software (e.g. Word).

```bash
Episode	Sn	PPV	TP	TN	FN	FP
Non-Artifact	0.667	0.667	2	1	1	1
Artifact	0.500	0.500	1	2	1	1

Total Event Count: 5

False Positive Artifact Events:
recording_1_events_1.json

False Negative Artifact Events:
recording_2_events_1.json
```

If you get an error on the term "match pair," it is because you need to update Python to version 3.10 or later.
One word of caution—this prints each value with tab characters in between, but some terminal emulators
automatically convert tabs to spaces. An alternative is to pipe the output directly into another text file.
For example, running:

```bash
python stats.py simple_predicted.json simple_actual.json > output.txt
```

does not print to standard out, instead placing the text in a file called `output.txt`. This preserves
the tab characters for later use.

If, instead, one wishes to display the output in a nicer format directly in the command-line, simply use the `-f`
(or `--formatted`) flag. stats.py also supports modifying the precision of the sensitivity and positive predictivity
through the `--precision` argument. For example, if one wanted to format the output in the terminal with
5 digits of precision, they would enter:

```bash
python stats.py simple_predicted.json simple_actual.json -f --precision 5
```

which would output:
```bash
Episode       Sn       PPV      TP  TN  FN  FP
Non-Artifact  0.66667  0.66667  2   1   1   1 
Artifact      0.50000  0.50000  1   2   1   1 

Total Event Count: 5

False Positive Artifact Events:
recording_1_events_1.json

False Negative Artifact Events:
recording_2_events_1.json
```

### Printing False Positive and False Negative Events

In addition to accuracy statistics, stats.py also prints out the recording and event numbers of 
false positive and false negative events for further model analysis. By default, this is set to print "Artifact" events,
but this can be changed using the `-a` (or `--arrhythmia`) argument. For example, if one wished to print out the
incorrectly classified non-artifact events in simple_predicted.json and simple_actual.json, they would enter:

```bash
python stats.py simple_predicted.json simple_actual.json -a Non-Artifact -f
```

which would output:

```bash
Episode       Sn     PPV    TP  TN  FN  FP
Non-Artifact  0.667  0.667  2   1   1   1 
Artifact      0.500  0.500  1   2   1   1 

Total Event Count: 5

False Positive Non-Artifact Events:
recording_2_events_1.json

False Negative Non-Artifact Events:
recording_1_events_1.json
```

If one wishes to print all or none of false positive and negative classifications,
use the `--print-all` or the `--print-none` flag respectively. In addition, stats.py prints out accuracy statistics
for all the classes provided in the actual JSON file's `labels` list (if not provided, one is constructed from the
unique classifications in the file). If one wanted to only print out statistics for one specific arrhythmia, use the `--single` flag.
For example, if one wished to print out statistics for only the normal classifications in full_predicted.json
and full_actual.json, but not print the recording or event numbers for the false classifications, they would enter:

```bash
python stats.py full_predicted.json full_actual.json -a Normal --single --print-none -f
```

which would output:

```bash
Episode      Sn     PPV    TP    TN    FN    FP  
Normal       0.733  0.648  1378  3078  503   748 

Total Event Count: 5707
```

### Analysis with Non-Text Classifications

stats.py also supports non-text arrhythmia classifications. The files numeric_predicted.json and numeric_actual.json are similar to
full_predicted.json and full_actual.json, but use numeric labels instead of text ones. To run it, all that one has to do is to make sure they
use the `-a` (or `--arrhythmia`) argument with their preferred arrhythmia. For example, to run the program with
the same specifications as the example above but with numeric_predicted.json and numeric_full.json, analyzing the 0 classification, they would run:

```bash
python stats.py numeric_predicted.json numeric_actual.json -a 0 --single --print-none -f
```

which would output:

```bash
Episode  Sn     PPV    TP    TN    FN    FP  
0        0.750  0.652  1410  3075  471   751 

Total Event Count: 5707
```

### Using Confidence Intervals to Increase Positive Predictive Values
Starting in v1.1, stats.py supports passing dictionaries of probabilities instead of pure classifications in the predicted file.
To use this feature, use the `-c` (pr `--confidence`) flag and pass in a value from 0 to 1 as the minimum confidence level.
For example, try:

```bash
python stats.py simple_prob_predicted.json simple_prob_actual.json -c .7 -f
```

Example JSON files

```json
{
  "recording_1_events_2.json": {
    "Non-Artifact": 0.9,
    "Artifact": 0.1
  },
  "recording_1_events_1.json": {
    "Non-Artifact": 0.3,
    "Artifact": 0.7
  },
  "recording_2_events_2.json": {
    "Non-Artifact": 0.7,
    "Artifact": 0.3
  },
  "recording_2_events_1.json": {
    "Non-Artifact": 0.6,
    "Artifact": 0.4
  },
  "recording_1_events_3.json": {
    "Non-Artifact": 0.1,
    "Artifact": 0.9
  }
}
```

```json
{
  "filenames": {
    "recording_1_events_1.json": "Non-Artifact",
    "recording_1_events_3.json": "Artifact",
    "recording_1_events_2.json": "Non-Artifact",
    "recording_2_events_2.json": "Non-Artifact",
    "recording_2_events_1.json": "Artifact"
  },

  "labels": ["Non-Artifact", "Artifact"]
}
```


which runs simple_prob_predicted.json and simple_prob_actual.json with a minimum confidence level of 70%. This outputs:

```bash
Episode       Sn     PPV    TP  TN  FN  FP
Non-Artifact  0.333  1.000  1   2   2   0 
Artifact      0.500  1.000  1   3   1   0 

Total Event Count: 5

False Positive Artifact Events:

False Negative Artifact Events:
recording_2_events_1.json
```

stats.py automatically chooses the classification with the highest probability. If this is lower than the passed
minimum confidence level, then the prediction is set to `None` instead of an actual classification. Increasing the
minimum value thereby increases PPV at the expense of decreasing Sn. Setting the minimum value to 0 causes the program
to simply take the predicted class with the highest probability.

All of the above parameters work when using confidence intervals, too. One example is shown below,
setting the minimum to 0 and using `--print-none`:

```bash
python stats.py full_prob_predicted.json full_prob_actual.json -c 0 -f --print-none
```

which would output:

```bash
Episode      Sn     PPV    TP     TN     FN     FP   
Artifact     0.858  0.887  8306   17803  1370   1054 
Normal       0.918  0.468  8631   9322   770    9810 
Bradycardia  0.005  1.000  4      27656  873    0    
Other        0.010  0.583  7      27811  710    5    
AF           0.062  0.888  373    22475  5638   47   
Tachycardia  0.148  0.926  274    26660  1577   22   

Total Event Count: 28533
```

To increase the PPV values of classifications like Artifact, simply set a higher minimum level:

```bash
python stats.py full_prob_predicted.json full_prob_actual.json -c 0.8 -f --print-none
```

which would output:

```bash
Episode      Sn     PPV    TP     TN     FN     FP   
Tachycardia  0.000  0.000  0      26682  1851   0    
AF           0.000  0.000  0      22522  6011   0    
Artifact     0.471  0.988  4553   18804  5123   53   
Bradycardia  0.000  0.000  0      27656  877    0    
Normal       0.039  0.981  364    19125  9037   7    
Other        0.000  0.000  0      27816  717    0    

Total Event Count: 28533
```

One warning when attempting to use numeric class labels with probabilities; JSON files only support strings as the keys
to dictionaries, so one must convert the numeric labels to strings.

### Acknowledgements

The GFU 2023-2024 Senior Design Team would like to thank TZ Medical for giving us the opportunity to work on this problem,
our advisors Brian Snider and Jillian Darrow for their advice and continued support,
and the GFU Engineering and Computer Science deparments for organizing the Senior Design class.