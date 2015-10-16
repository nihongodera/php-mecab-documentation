# php-mecab Documentation   
Documentation for the package [rsky/php-mecab](https://github.com/rsky/php-mecab).   

## Contents  
  - [Installation](#installation)
  - [Basic Usage](#basic-usage)
  - [Classes](#classes)
    - [MeCab_Tagger](#mecab_tagger)
    - [MeCab_Node](#mecab_node)

## Installation   
Coming...   

## Basic Usage   
Once installed, you can new up a MeCab_Tagger object, passing a dictionary folder path to it as a parameter.   
```
$mecab = new MeCab_Tagger('-d', '/var/lib/mecab/dic/ipadic-utf8');   
```   
'-d' is a console flag telling mecab that you are passing it a dictionary directory.  The location of the directory seems to vary by system, so find 'ipadic-utf8' on your system and pass the full folder path.  Often, there will be more than one 'ipadic-utf8' folder on a system.  Make sure the one you use contains a file called 'unk.dic'.  Without this, mecab will fail to initialize.

Once you have a MeCab_Tagger instance, you can start parsing.  Three methods are useful for basic parsing.    

#### parse($string)  
Parses the string and returns the Mecab results as a string.   
```
$results = $mecab->parse('行きます');

echo $results;
```
Will produce:
```
行き    動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ
ます    助動詞,*,*,*,特殊・マス,基本形,ます,マス,マス
EOS
```

#### parseToNode($string)  
Parses the string and returns the Mecab results as an instance of MeCab_Node.  
```
$node = $this->mecab->makeNodes($this->text);

foreach ($node as $line) {
    echo $line->feature;
}
```
Will produce:
```
BOS/EOS,*,*,*,*,*,*,*,*動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ助動詞,*,*,*,特殊・マス,基本形,ます,マス,マスBOS/EOS,*,*,*,*,*,*,*,*
```  

#### mecab_split($string, $dictionary_directory_path)  
Rather than be on the MeCab_Tagger object, this method is a global with no connection to the object we made earlier.  Therefore, it requires the dictionary directory path to function.  Yeah, I know.  I don't get it either.  
This method only returns an array of the text pieces with no Mecab info.
```
$results = mecab_split('行きます', '/var/lib/mecab/dic/ipadic-utf8');

print_r($results);
```   
Will produce:
```
Array
(
    [0] => 行き
    [1] => ます
)
```

## Classes   

### MeCab_Tagger   
Main class used to parse text.   
#### Methods
  - version()
  - split()
  - __construct()
  - getPartial()
  - setPartial()
  - getTheta()
  - setTheta()
  - getLatticeLevel()
  - setLatticeLevel()
  - getAllMorphs()
  - setAllMorphs()
  - parse()
  - parseToString()
  - parseToNode()
  - parseNBest()
  - parseNBestInit()
  - next()
  - nextNode()
  - formatNode()
  - dictionaryInfo()

### Mecab_Node
Returned by parseToNode method on Mecab_Tagger.
#### Methods
  - __get()
  - __isset()
  - getIterator()
  - setTraverse()
  - toArray()
  - toString()
  - __toString()
  - getPrev()
  - getNext()
  - getENext()
  - getBNext()
  - getRPath()
  - getLPath()
  - getSurface()
  - getFeature()
  - getId()
  - getLength()
  - getRLength()
  - getRcAttr()
  - getLcAttr()
  - getPosId()
  - getCharType()
  - getStat()
  - isBest()
  - getAlpha()
  - getBeta()
  - getWCost()
  - getCost()

