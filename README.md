# php-mecab Documentation   
Documentation for the package [rsky/php-mecab](https://github.com/rsky/php-mecab).

## Contents  
  - [Installation](#installation)
  - [Basic Usage](#basic-usage)
  - [Classes and Methods](#classes-and-methods)
    - [MeCab_Tagger](#mecab_tagger)
    - [MeCab_Node](#mecab_node)

## Installation   
Coming...   

## Basic Usage   
Once installed, you can new up a MeCab_Tagger object, passing a dictionary folder path to it as a parameter.   
```
$mecab = new \MeCab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);   
```   
'-d' is a console flag telling mecab that you are passing it a dictionary directory.  The location of the directory seems to vary by system, so find 'ipadic-utf8' on your system and pass the full folder path.  Often, there will be more than one 'ipadic-utf8' folder on a system.  Make sure the one you use contains a file called 'unk.dic'.  Without this, mecab will fail to initialize.

Once you have a MeCab_Tagger instance, you can start parsing.  Three methods are useful for basic parsing.    

#### parse(string)  
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

#### parseToNode(string)  
Parses the string and returns the Mecab results as an instance of MeCab_Node.  
```
$node = $mecab->makeNodes($this->text);

foreach ($node as $line) {
    echo $line->feature;
}
```
Will produce:
```
BOS/EOS,*,*,*,*,*,*,*,*動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ助動詞,*,*,*,特殊・マス,基本形,ます,マス,マスBOS/EOS,*,*,*,*,*,*,*,*
```  

#### split(string, dictionary_directory_path)  
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

## Classes and Methods  
Based off documentation found here: [http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html](http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html)

### MeCab_Tagger   
Main class used to parse text.   
#### Methods
  - ##### version() [static]  
  Return Mecab version.

  - ##### split(string, dic_dir, user_dic, filter, persistent) [static]
  Split string into array of morphemes.  Usually requires the dictionary directory to be passed as a parameter.
  ```
  @param    string          string          The string to split   
  @param    string          dic_dir         Path to dictionary directory (optional)    
  @param    string          user_dic        Path to user dictionary (optional)   
  @param    callback        filter          Filter function or method (optional)      
  @param    boolean         persistent      (optional)    

  @return   array 
  ```
  Example  
  ```
   $mecab = new \Mecab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

   $array = $mecab::split('行きます', '/var/lib/mecab/dic/ipadic-utf8');

   print_r($array);

   Array
   (
     [0] => 行き
     [1] => ます
   )   
  ```


  - ##### __construct(arg, persistent)
  Construct class instance.
  ###### Parameters (2)  
    arg         array or NULL   [optional]   
    persistent  boolean         [optional]

  - ##### getPartial()  

  - ##### setPartial()  
  ###### Parameters  
    Parameter #0 [ <optional> $partial ]  

  - ##### getTheta()  

  - ##### setTheta()  
  ###### Parameters
    Parameter #0 [ <optional> $theta ]  

  - ##### getLatticeLevel()  

  - ##### setLatticeLevel()  
  ###### Parameters  
    Parameter #0 [ <optional> $level ]  

  - ##### getAllMorphs()  

  - ##### setAllMorphs()  
  ###### Parameters
    Parameter #0 [ <optional> $all_morphs ]  

  - ##### parse()  
  ###### Parameters
    Parameter #0 [ <required> $str ]  
    Parameter #1 [ <optional> $len ]  
    Parameter #2 [ <optional> $olen ]  

  - ##### parseToString()  
  ###### Parameters
    Parameter #0 [ <required> $str ]  
    Parameter #1 [ <optional> $len ]  
    Parameter #2 [ <optional> $olen ]  

  - ##### parseToNode()  
  ###### Parameters  
    Parameter #0 [ <required> $str ]  
    Parameter #1 [ <optional> $len ]  


  - ##### parseNBest()  
  ###### Parameters  
    Parameter #0 [ <required> $n ]  
    Parameter #1 [ <required> $str ]  
    Parameter #2 [ <optional> $len ]  
    Parameter #3 [ <optional> $olen ]  

  - ##### parseNBestInit()  
  ###### Parameters  
    Parameter #0 [ <required> $str ]  
    Parameter #1 [ <optional> $len ]  

  - ##### next()  
  ###### Parameters  
    Parameter #0 [ <optional> $olen ]  

  - ##### nextNode()  

  - ##### formatNode()  
  ###### Parameters  
    Parameter #0 [ <required> MeCab_Node $node ]  

  - ##### dictionaryInfo()

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

