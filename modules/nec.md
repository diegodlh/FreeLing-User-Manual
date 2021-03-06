
# Named Entity Classification Module {#named-entity-classification-module}

The mission of the Named Entity Classification module is to assing a class to named entities in the text. It is a Machine-Learning based module, so the classes can be anything the model has been trained to recognize.

When classified, the PoS tag of the word is changed to the label defined in the model.

This module depends on a NER module being applied previously. If no entities are recognized, none can be classified.

Models provided with FreeLing distinguish four classes: Person (tag <tt>NP00SP0</tt>), Geographical location (<tt>NP00G00</tt>), Organization (<tt>NP00O00</tt>), and Others (<tt>NP00V00</tt>).

The models can be retrained using an annotated corpus and the scripts in `src/utilities/nerc`.

See file [`src/utilities/train-nerc/README`](https://github.com/TALP-UPC/FreeLing/blob/master/src/utilities/train-nerc/README) and the comments inside the script for details on how to retrain the models or about the meaning of each file.

The most important file in the set is the `.rgf` file, which contains a definition of the context features that must be extracted for each named entity. The feature rule language is described in section [Feature Extractor](fex.md).

The API of the class is the following:

```C++
class nec {
  public:
    /// Constructor
    nec(const std::string &cfgfile); 

    /// analyze given sentence.
    void analyze(sentence &s) const;

    /// analyze given sentences.
    void analyze(std::list<sentence> &ls) const;

    /// return analyzed copy of given sentence
    sentence analyze(const sentence &s) const;

    /// return analyzed copy of given sentences
    std::list<sentence> analyze(const std::list<sentence> &ls) const;
};
```

The constructor receives one parameter with the name of the configuration file for the module. Its content is described below.

## NEC Data File {#nec-data-file}

The machine-learning based Named Entity Classification module reads a configuration file with the following sections

*   Section `<RGF>` contains one line with the path to the RGF file of the model. This file is the definition of the features that will be taken into account for NEC.
    ```XML
    <RGF>
    ner.rgf
    </RGF>
    ```

*   Section `<Classifier>` contains one line with the kind of classifier to use. Valid values are `AdaBoost` and `SVM`.
    ```
    <Classifier>
    Adaboost
    </Classifier>
    ```


*   Section `<ModelFile>` contains one line with the path to the model file to be used. The model file must match the classifier type given in section `<Classifier>`.

    ```
    <ModelFile>
    ner.abm
    </ModelFile>
    ```

    The `.abm<` files contain AdaBoost models based on shallow Decision Trees (see [\[CMP03\]](../references.md) for details). You don't need to understand this, unless you want to enter into the code of the AdaBoost classifier.

    The `.svm` files contain Support Vector Machine models generated by <tt>libsvm</tt>  [\[CL11\]](../references.md). You don't need to understand this, unless you want to enter into the code of <tt>libsvm</tt>.

*   Section `<Lexicon>` contains one line with the path to the lexicon file of the learnt model. The lexicon is used to translate string-encoded features generated by the feature extractor to integer-encoded features needed by the classifier. The lexicon file is generated at training time.
    ```
    <Lexicon>
    ner.lex
    </Lexicon>
    ```

    The `.lex` file is a dictionary that assigns a number to each symbolic feature used in the AdaBoost or SVM model. You don't need to understand this either unless you are a Machine Learning student or the like.

*   Section `<Classes>` contains only one line with the classes of the model and its translation to <tt>B</tt>, <tt>I</tt>, <tt>O</tt> tag.
    ```
    <Classes>
    0 NP00SP0 1 NP00G00 2 NP00O00 3 NP00V00
    </Classes>
    ```

*   Section `<NE_Tag>` contains only one line with the PoS tag assigned by the NER module, which will be used to select named entities to be classified.
    ```
    <NE_Tag>
    NP00000
    </NE_Tag>
    ```
