# php-mecab Documentation   
Documentation for the package [rsky/php-mecab](https://github.com/rsky/php-mecab).

## Contents  
  - [Installation](#installation)
  - [Usage](#usage)
    - [Initialization](#initialization)
  - [Classes and Functions](#classes-and-functions)
    - [Classes](#classes)
      - [MeCab_Tagger](#mecab_tagger)
      - [MeCab_Node](#mecab_node)
      - [Mecab_Path](#mecab_path)
      - [MeCab_NodeIterator](#mecab_nodeiterator)
    - [Functions](#functions)
  - [Other Resources](#other-resources)

## Installation   
Coming...   

[Top](#contents)

## Usage   
php-mecab can be used functionally or as an object.  I prefer the OOP approach, but I will try to cover both approaches in this guide.

### Initialization
Once installed, you can new up a MeCab_Tagger object, passing an array containing a command line flag and a dictionary folder path to it as a parameter.   
```
$mecab = new \MeCab_Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);   
```   
The variable $mecab will be a MeCab_Tagger object.  
  
'-d' is a console flag telling mecab that you are passing it a dictionary directory.  The location of the directory seems to vary by system, so find 'ipadic-utf8' on your system and pass the full folder path.  Often, there will be more than one 'ipadic-utf8' folders on a system.  Make sure the one you use contains a file called 'unk.dic'.  Without this, mecab will fail to initialize.

You can also use the function mecab_new().
```
$mecab = mecab_new(['-d', '/var/lib/mecab/dic/ipadic-utf8']);
```  
In this case, $mecab will be a resources of type 'mecab'.

[Top](#contents)

### Parsing

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

[Top](#contents)

## Classes and Functions  
### Classes
  - [MeCab_Tagger](#mecab_tagger)
  - [MeCab_Node](#mecab_node)
  - [Mecab_Path](#mecab_path)
  - [MeCab_NodeIterator](#mecab_nodeiterator)

#### MeCab_Tagger   
Main class used to parse text.   
##### Methods
  - [version()](#version-static)
  - [split()](#splitstring-dic_dir-user_dic-filter-persistent-static)
  - [__construct()](#__constructarguments-persistent)
  - [getPartial()](#getpartial)
  - [setPartial()](#setpartialbool)
  - [getTheta()](#gettheta)
  - [setTheta()](#setthetatheta)
  - [getLatticeLevel()](#getlatticelevel)
  - [setLatticeLevel()](#setlatticelevellevel)
  - [getAllMorphs()](#getallmorphs)
  - [setAllMorphs()](#setallmorphsbool)
  - [parse()](#parsestring-length-output_length)
  - [parseToString()](#parsetostringstring-length-output_length)
  - [parseToNode()](#parsetonodestring-length)
  - [parseNBest()](#parsenbestn-string-length-output_length)
  - [parseNBestInit()](#parsenbestinitstring-length)
  - [next()](#nextoutput_length)
  - [nextNode()](#nextnode)
  - [formatNode()](#formatnodenode)
  - [dictionaryInfo()](#dictionaryinfo)

###### version() [static]  
Return Mecab version.
```php
* @return   string
```

###### split($string, $dic_dir, $user_dic, $filter, $persistent) [static]
Split string into array of morphemes.  Usually requires the dictionary directory to be passed as a parameter.
```
* @param    string        $string         String to split.  
* @param    string        $dic_dir        Path to dictionary directory. (Optional)    
* @param    string        $user_dic       Path to user dictionary. (Optional)   
* @param    callback      $filter         Filter function or method. (Optional)      
* @param    boolean       $persistent     (Optional)    
*
* @return   array 
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

###### __construct($arguments, $persistent)
Construct class instance.
```
* @param    array         $arguments      Command line arguments.        
* @param    boolean       $persistent     (Optional)    
*
* @return     MeCab_Tagger 
```

###### getPartial()  
Get current partial parsing mode state.  
```
* @return     boolean
```

###### setPartial($bool)  
Set partial parsing mode.  
```
* @param      boolean     $bool           Partial parsing mode.
```

###### getTheta()  
Get current temparature parameter theta.  
```
* @return     float
```

###### setTheta($theta)  
Set temparature parameter theta.
```
* @param      float/int   $theta          Temparature parameter theta.
```

###### getLatticeLevel()  
Get current lattice level.
```
* @return     int
```

###### setLatticeLevel($level)  
Set lattice level.
```
* @param      int         $level          Lattice level.
```

###### getAllMorphs()  
Get all-morphs output mode.
```
* @return     bool
```

###### setAllMorphs($bool)  
Set all-morphs output mode.
```
* @param      bool        $bool           All-morphs output mode.
```

###### parse($string, $length, $output_length)  
Parse string and output results as string.
```
* @param      string      $string         String to be parsed.
* @param      int         $length         Length to be analyzed. (Optional)  
* @param      int         $output_length  Maximum length of output. (Optional)
*
* @return     string
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

###### parseToString($string, $length, $output_length)  
Parse string and output results as string.
```
* @param      string      $string         String to be parsed.
* @param      int         $length         Length to be analyzed. (Optional)  
* @param      int         $output_length  Maximum length of output. (Optional)
*
* @return     string
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

###### parseToNode($string, $length)  
Parse string and output results as MeCab_Node.
```
* @param      string      $string         String to be parsed.
* @param      int         $length         Length to be analyzed. (Optional)
*
* @return     MeCab_Node
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

###### parseNBest($n, $string, $length, $output_length)  
Parse given sentence and output N-best results as string.  This method causes seg faults for me.
```
* @param      int         $n              Number of results to obtain.
* @param      string      $string         String to be parsed.
* @param      int         $length         Length to be analyzed. (Optional)
* @param      int         $output_length  Maximum length of output. (Optional)
*
* @return     string
```

###### parseNBestInit($string, $length)  
Initialize N-best enumeration with a sentence. 
```
* @param      string      $string         String to be parsed.
* @param      int         $length         Length to be analyzed. (Optional)

* @return     boolean
```

###### next($output_length)  
Get the next result of N-Best as a string.
```
* @param      int         $output_length  Maximum length of output. (Optional)
*
* @return     string
```

###### nextNode()  
Get the next result of N-Best as a node.
```
* @return     MeCab_Node
```

###### formatNode($node)  
Format a node to a string.
```
* @param      MeCab_Node  $node           Node to be formatted.
*
* @return     string
```

###### dictionaryInfo()
Return array of dictionary info.
```
* @return     array
```

#### Mecab_Node
Returned by parseToNode method on Mecab_Tagger.
##### Methods
  - [getIterator()](#getiterator)
  - [setTraverse()](#settraversemode)
  - [getPrev()](#getprev)
  - [getNext()](#getnext)
  - [getENext()](#getenext)
  - [getBNext()](#getbnext)
  - [getRPath()](#getrpath)
  - [getLPath()](#getlpath)
  - [getSurface()](#getsurface)
  - [getFeature()](#getfeature)
  - [getId()](#getid)
  - [getLength()](#getlength)
  - [getRLength()](#getrlength)
  - [getRcAttr()](#getrcattr)
  - [getLcAttr()](#getlcattr)
  - [getPosId()](#getposid)
  - [getCharType()](#getchartype)
  - [getStat()](#getstat)
  - [getAlpha()](#getalpha)
  - [getBeta()](#getbeta)
  - [getWCost()](#getwcost)
  - [getCost()](#getcost)
  - [getProb()](#getprob)
  - [isBest()](#isbest)
  - [toArray()](#toarraydump_all)
  - [toString()](#tostring)

###### getIterator()
Return MeCab_NodeIterator.
```
* @return     MeCab_NodeIterator
```

###### setTraverse($mode)
Set the traverse mode.
```
* @param      long        $mode           Traverse mode.
```

###### getPrev()
Get the previous node.  Return NULL if none.
```
* @return     MeCab_Node
```

###### getNext()
Get the next node.  Return NULL if none.
```
* @return     MeCab_Node
```

###### getENext()
Get the next node which has same end point as the given node.  Return NULL if none.
```
* @return     MeCab_Node
```

###### getBNext()
Get the next node which has same beginning point as the given node.  Return NULL if none.
```
* @return     MeCab_Node
```

###### getRPath()
Get the next node which has same end point as the given node.  Return NULL if none.
```
* @return     MeCab_Path
```

###### getLPath()
Get the next node which has same beginning point as the given node.  Return NULL if none.
```
* @return     MeCab_Path
```

###### getSurface()
Get the surface of the node.
```
* @return     string
```

###### getFeature()  
Get the feature of the node.
```
* @return     string
```

###### getId()  
Get the ID of the node.
```
* @return     int
```

###### getLength()
Get the length of the node's surface.
```
* @return     int
```

###### getRLength()  
Get the length of the node's surface including it's leading whitespace.
```
* @return     int
```

###### getRcAttr()
Get the ID of the right context.
```
* @return     int
```

###### getLcAttr()
Get the ID of the left context.
```
* @return     int
```

###### getPosId()
Get the ID of the part of speech.
```
* @return     int
```

###### getCharType()
Get the type of character.
```
* @return     int
```

###### getStat()
Get the status of the node.
```
* @return     int
```
0: Normal, MECAB_NOR_NODE  
1: Unknown, MECAB_UNK_NODE  
2: Beginning of Sentence, MECAB_BOS_NODE  
3: End of Sentence, MECAB_EOS_NODE  

###### getAlpha()
Get the forward log probability.
```
* @return     float
```

###### getBeta()
Get the backward probability log.
```
* @return     float
```

###### getWCost()
Get the word arising cost.
```
* @return     int
```

###### getCost()
Get the cumulative cost of the node. 
```
* @return     int
```

###### getProb()
Get the marginal probability of the node.
```
* @return     float
```

###### isBest()
Determine whether the node is the best solution.
```
* @return     boolean
```

###### toArray($dump_all)
Get all elements of the node as an associative array.
```
* @param      boolean     $dump_all       Dump all related nodes if true. (Optional)

* @return     array
```

###### toString()
Get the formatted string of the node.
```
* @return     string
```

#### MeCab_Path
Returned by getRPath and getLPath methods on MeCab_Node class.
##### Methods
 - [getRNext()](#getrnext)
 - [getLNext()](#getlnext)
 - [getRNode()](#getrnode)
 - [getLNode()](#getlnode)
 - [getProb()](#getprob-1)
 - [getCost()](#getcost-1)

###### getRNext()
Get the rnext path. Return NULL if none.
```
* @return     MeCab_Path
```

###### getLNext()
Get the lext path. Return NULL if none.
```
* @return     MeCab_Path
```

###### getRNode()
Get the rnode. Return NULL if none.
```
* @return     MeCab_Node
```

###### getLNode()
Get the lnode. Return NULL if none.
```
* @return     MeCab_Node
```

###### getProb()
Get the marginal probability of the path.
```
* @return     float
```

###### getCost()
Get the cumulative cost of the path. 
```
* @return     int
```

#### MeCab_NodeIterator
Node iterator class.
##### Methods
  - [current()](#current)
  - [key()](#key)
  - [next()](#next)
  - [rewind()](#rewind)
  - [valid()](#valid)

###### current()
Return the current element.
```
* @return     MeCab_Node
```

###### key()
```
* @return     int
```

###### next()
Set pointer to next element.

- ###### rewind()
Set pointer to beginning.

###### valid()
Check if there is a current element after calls to rewind() or next().
```
* @return     boolean
```

### Functions
  - [mecab_version()](#mecab_version)

###### mecab_version()
Return Mecab version.
Return Mecab version.
```
* @return   string
```

###### 

[Top](#contents)

## Other Resources
The University of the Ryukyus Department of Mechanical Systems Engineering maintains a php-mecab API documentation page that can be useful.   
[http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html](http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html)    
  
The MeCab documentation is here on github, but its in Japanese only.   
[http://taku910.github.io/mecab/](http://taku910.github.io/mecab/)    
  
The MeCab api documentation is up on googlecode.  
[https://mecab.googlecode.com/svn/trunk/mecab/doc/doxygen/index.html](https://mecab.googlecode.com/svn/trunk/mecab/doc/doxygen/index.html)
  
If you're using an IDE, fumikito has a gist that can help with php-mecab class recognition.  
[https://gist.github.com/fumikito/bb172b4cf5648c7f8451](https://gist.github.com/fumikito/bb172b4cf5648c7f8451)

[Top](#contents)
