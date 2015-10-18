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

### MeCab_Tagger   
Main class used to parse text.   
#### Methods
  - ##### version() [static]  
  Return Mecab version.

  - ##### split(string, dic_dir, user_dic, filter, persistent) [static]
  Split string into array of morphemes.  Usually requires the dictionary directory to be passed as a parameter.
  ```
  @param    string          string          String to split.  
  @param    string          dic_dir         Path to dictionary directory. (Optional)    
  @param    string          user_dic        Path to user dictionary. (Optional)   
  @param    callback        filter          Filter function or method. (Optional)      
  @param    boolean         persistent      (Optional)    

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


  - ##### __construct(arguments, persistent)
  Construct class instance.
  ```
  @param    array           arguments       Command line arguments.        
  @param    boolean         persistent      (Optional)    

  @return   MeCab_Tagger 
  ```

  - ##### getPartial()  
  Get current partial parsing mode state.  
  ```
  @return   boolean
  ```

  - ##### setPartial(bool)  
  Set partial parsing mode.  
  ```
  @param    boolean          bool           Partial parsing mode.
  ```

  - ##### getTheta()  
  Get current temparature parameter theta.  
  ```
  return    float
  ```

  - ##### setTheta(theta)  
  Set temparature parameter theta.
  ```
  @param    float/int       theta           Temparature parameter theta.
  ```

  - ##### getLatticeLevel()  
  Get current lattice level.
  ```
  @return   int
  ```

  - ##### setLatticeLevel(level)  
  Set lattice level.
  ```
  @param    int             level           Lattice level.
  ```

  - ##### getAllMorphs()  
  Get all-morphs output mode.
  ```
  @return   bool
  ```

  - ##### setAllMorphs(bool)  
  Set all-morphs output mode.
  ```
  @param    bool            bool            All-morphs output mode.
  ```

  - ##### parse(string, length, output_length)  
  Parse string and output results as string.
  ```
  @param    string          string          String to be parsed.
  @param    int             length          Length to be analyzed. (Optional)  
  @param    int             output_length   Maximum length of output. (Optional)

  @return   string
  ```
  Example
  ```
  $mecab = new \Mecab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

  $string = $mecab->parse('行きます');

  print_r($string);

  行き    動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ
  ます    助動詞,*,*,*,特殊・マス,基本形,ます,マス,マス
  EOS
  ```

  - ##### parseToString(string, length, output_length)  
  Parse string and output results as string.
  ```
  @param    string          string          String to be parsed.
  @param    int             length          Length to be analyzed. (Optional)  
  @param    int             output_length   Maximum length of output. (Optional)

  @return   string
  ```
  Example
  ```
  $mecab = new \Mecab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

  $string = $mecab->parseToString('行きます');

  print_r($string);

  行き    動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ
  ます    助動詞,*,*,*,特殊・マス,基本形,ます,マス,マス
  EOS
  ```

  - ##### parseToNode(string, length)  
  Parse string and output results as MeCab_Node.
  ```
  @param    string          string          String to be parsed.
  @param    int             length          Length to be analyzed. (Optional)

  @return   MeCab_Node
  ```
  Example
  ```
  $mecab = new \Mecab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

  $node = $mecab->parseToNode('行きます');

  print_r($node->toArray());

  Array
  (
    [surface] => 
    [feature] => BOS/EOS,*,*,*,*,*,*,*,*
    [id] => 0
    [length] => 0
    [rlength] => 0
    [rcAttr] => 0
    [lcAttr] => 0
    [posid] => 0
    [char_type] => 0
    [stat] => 2
    [isbest] => 1
    [alpha] => 0
    [beta] => 0
    [prob] => 0
    [wcost] => 0
    [cost] => 0
  )
  ```

  - ##### parseNBest(n, string, length, output_length)  
  Parse given sentence and output N-best results as string.  This method causes seg faults for me.
  ```
  @param    int             n               Number of results to obtain.
  @param    string          string          String to be parsed.
  @param    int             length          Length to be analyzed. (Optional)
  @param    int             output_length   Maximum length of output. (Optional)

  @return   string
  ```

  - ##### parseNBestInit(string, length)  
  Initialize N-best enumeration with a sentence. 
  ```
  @param    string          string          String to be parsed.
  @param    int             length          Length to be analyzed. (Optional)

  @return   boolean
  ```

  - ##### next(output_length)  
  Get the next result of N-Best as a string.
  ```
  @param    int             output_length   Maximum length of output. (Optional)

  @return   string
  ```

  - ##### nextNode()  
  Get the next result of N-Best as a node.
  ```
  @return   MeCab_Node
  ```

  - ##### formatNode(node)  
  Format a node to a string.
  ```
  @param    MeCab_Node      node            Node to be formatted.

  @return   string
  ```

  - ##### dictionaryInfo()
  Return array of dictionary info.
  ```
  @return   array
  ```

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

