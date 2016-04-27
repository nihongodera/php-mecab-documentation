# php-mecab Documentation   
Documentation for the package [rsky/php-mecab](https://github.com/rsky/php-mecab).

## Contents  
  - [Installation](#installation)
  - [Usage](#usage)
    - [Initialization](#initialization)
    - [Splitting Strings](#splitting-strings)
    - [Parsing Strings](#parsing-strings)
    - [Using Nodes](#using-nodes)
    - [Basic MeCab](#basic-mecab)
  - [Classes and Functions](#classes-and-functions)
    - [Classes](#classes)
      - [MeCab\Tagger](#mecab\tagger)
      - [MeCab\Node](#mecab\node)
      - [Mecab\Path](#mecab\path)
      - [MeCab\NodeIterator](#mecab\nodeiterator)
    - [Functions](#functions)
  - [Other Resources](#other-resources)
  - [Contributing](#contributing)

## Installation   
(Please note that I am a Linux user and have only tested the Linux installation guide.  The Mac and Windows installation guides have been pieced together from other sources.)  
   
Ubuntu users can use the install script included in this repository to install mecab and php-mecab.    
Download the script:
```
curl -O https://raw.githubusercontent.com/nihongodera/php-mecab-documentation/master/install-php-mecab.sh
```
Make the file executable:
```
chmod +x install-php-mecab.sh
```
Execute the script:
```
./install-php-mecab.sh
```
For information about what the script does, see [here](https://github.com/nihongodera/limelight/wiki/Install-Script).  

### Install MeCab
Before installing php-mecab, you must install MeCab.

#### Linux
Linux users can more than likely find MeCab in their distro repositories.  Simply install 'mecab' and the package 'mecab-ipadic-utf8'.  Ubuntu users can do this with the following command.
```
sudo apt-get install mecab mecab-ipadic-utf8
```

If that doesn't work, you can download the source and build it yourself.  Note that this will require the package 'build-essential'.  
First pull in MeCab.
```
wget https://mecab.googlecode.com/files/mecab-0.996.tar.gz
tar zxfv mecab-0.996.tar.gz
cd mecab-0.996
./configure --with-charset=utf8 --enable-utf8-only
```
Then get the dictionary file.
```
wget https://mecab.googlecode.com/files/mecab-ipadic-2.7.0-20070801.tar.gz
tar zxfv mecab-ipadic-2.7.0-20070801.tar.gz
cd mecab-ipadic-2.7.0-20070801
./configure --with-charset=utf8
```

#### Mac OS X
Both MeCab and the required dictionary (mecab-ipadic-utf8) are in MacPorts.  If that doesn't work, try downloading the source and building it yourself.  You can get the source and the dictionary from the following urls:  
https://mecab.googlecode.com/files/mecab-0.996.tar.gz  
https://mecab.googlecode.com/files/mecab-ipadic-2.7.0-20070801.tar.gz

I believe you can build these files with Xcode.  Somebody correct me if I'm wrong.

#### Windows
Download the installer from this url: https://mecab.googlecode.com/files/mecab-0.996.exe  

### Install php-mecab
First, verify that you have MeCab on your computer by testing it in the command line.  Type `mecab` and if you don't get an error, things are looking good.  If you get an error that looks something like this `param.cpp(69) [ifs] no such file or directory: /usr/local/lib/mecab/dic/ipadic/dicrc` you need to find your dictionary file and pass it as a parameter.  The directory is called 'ipadic-utf8' and needs to contain a file called 'unk.dic'.
```
mecab --dicdir=/path/to/dictionary/dic/ipadic/
```
Once you get mecab to start, type some Japanese and make sure you get an appropriate response.
```
~$ mecab
やった！
やっ    動詞,自立,*,*,五段・ラ行,連用タ接続,やる,ヤッ,ヤッ
た      助動詞,*,*,*,特殊・タ,基本形,た,タ,タ
！      記号,一般,*,*,*,*,！,！,！
EOS
```

#### Linux
Install the following dependencies:  
php5:    
php5-dev
libmecab-dev  
build-essential  
```
sudo apt-get install php5-dev libmecab-dev build-essential
```  
php7:    
php7.0-dev
libmecab-dev  
build-essential  
```
sudo apt-get install php7.0-dev libmecab-dev build-essential
```  

Download the php-mecab source.
```
wget https://github.com/rsky/php-mecab/archive/master.zip
```

You will need to find the package 'mecab-config'.  It is usually located at /usr/bin/mecab-config, but check to make sure. Let's use 'locate' because its easy.
```
sudo updatedb
locate mecab-config
```
That should give you a path that looks something like /usr/bin/mecab-config.  
We should now be ready to build our package.  Put your mecab-config path after the --with-mecab-config option.  
```
unzip master.zip
cd php-mecab-master/mecab
phpize
sudo ./configure --with-php-config=/usr/bin/php-config --with-mecab-config=/path/to/mecab-config
sudo make
sudo make install
```

Occasionally, configure will fail and throw the following error:
```
configure: error: wrong MeCab library version or lib not found. Check config.log for more information
```

This usually happens when mecab didn't install properly. To fix this, purge all mecab packages:
```
sudo apt-get --purge remove mecab mecab-ipadic-utf8 mecab-utils libmecab-dev
```
This will often not remove all the binaries so you may have to manually go into bin and remove them yourself.
```
sudo rm /usr/local/bin/mecab
sudo rm /usr/local/bin/mecab-config
```
Then, reinstall everything:
```
sudo apt-get install mecab mecab-ipadic-utf8 mecab-utils libmecab-dev
```

After completing this step, you should have a mecab.so.  Go to /usr/lib/php5/ and find the package with a name that looks is similar to this: 20131226. Have a look in that file and mecab.so should be in there.

We now just need to enable the mod.  
For php5:    
Move to /etc/php5/mods-available/
```
cd /etc/php5/mods-available/
```
Next, create a new .ini file for mecab.
```
sudo touch mecab.ini
echo "extension=mecab.so" | sudo tee -a mecab.ini
```
And then we need to activate the module.
```
sudo php5enmod mecab
``` 
___

For php7:    
Move to /etc/php/php7.0/mods-available/
```
cd /etc/php/php7.0/mods-available/
```
Next, create a new .ini file for mecab.
```
sudo touch mecab.ini
echo "extension=mecab.so" | sudo tee -a mecab.ini
```
And then we need to activate the module.
```
sudo phpenmod -v 7.0 mecab
``` 
___

Once this is done, you simply need to restart your web server.  
For Apache:
```
sudo service apache2 restart
```
And for nginx:
```
sudo service nginx restart
```

You should be ready to go. 

#### Mac OS X
Instructions should be the same as for Linux, but you may require the package xcode in order to properly compile the source code.

#### Windows
Installing php-mecab is the same as installing any other php extension.  The following guide may be of use:  
http://php.net/manual/en/install.windows.extensions.php  

According to one of the php-mecab readme files:
>The extension provides the VisualStudio V6 project file mecab.dsp.
>To compile the extension you open this file using VisualStudio,
>select the apropriate configuration for your installation
>(either "Release_TS" or "Debug_TS") and create "php_mecab.dll"
>
>After successfull compilation you have to copy the newly
>created "php_mecab.dll" to the PHP
>extension directory (default: C:\PHP\extensions).

[Top](#contents)

## Usage   
php-mecab can be used functionally or as an object.  I prefer the OOP approach, but I will try to cover both approaches in this guide. Note that as of version 0.6.0, the procedural functions will not work in php 7.
  - [Initialization](#initialization)
  - [Splitting Strings](#splitting-strings)
  - [Parsing Strings](#parsing-strings)
  - [Using Nodes](#using-nodes)
  - [Basic MeCab](#basic-mecab)

### Initialization
MeCab sometimes requires a dictionary directory to be passed to it on initialization.  The location of the directory seems to vary by system, so find 'ipadic-utf8' on your system and pass the full folder path.  Often, there will be more than one 'ipadic-utf8' folders on a system.  Make sure the one you use contains a file called 'unk.dic'.  Without this, mecab will fail to initialize.  Pass the the dictionary directory to MeCab with the console flag '-d' in an array.   
   
The options passed to MeCab are the same as the options used in the command line program.  Send them to the constructor in an array.  Check the man page for MeCab for all available options.   

#### Object Orientated
New up a [MeCab\Tagger](#__constructarguments-persistent) object.
Version 0.6.0:
```php
$mecab = new \MeCab\Tagger();   
```
Earlier versions:
```php
$mecab = new \MeCab_Tagger();   
```
If it does't work, or you get an error, try passing the array containing the command line flag '-d' and a dictionary folder path to it as a parameter.   
```php
$mecab = new \MeCab\Tagger(['-d', '/path/to/dictionary/mecab/dic/ipadic-utf8']);   
```   
The variable $mecab will be a [MeCab\Tagger](#mecab\tagger) object.  
**Throughout this guide, when I refer to *$mecab* in the object orientated sections, it will be a Tagger object.**

#### Functional
Use the function [mecab_new()](#mecab_newarguments-persistent) to get a mecab resource.  As with the Object Orientated approach, you may or may not have to pass it a dictionary directory.
```php
$mecab = mecab_new(['-d', '/path/to/dictionary/mecab/dic/ipadic-utf8']);
```  
The $mecab variable will be a resource of type 'mecab'.   
**Throughout this guide, when I refer to *$mecab* in the functional sections, it will be a MeCab resource.**

[Top](#contents)

### Splitting Strings
Split methods only split a string into an array of morphemes.  They provide no information about the morphemes.

#### Object Orientated
As of version 0.6.0, the split method is no longer on the Tagger object. The following only applies to previous versions.    
The [split()](#splitstring-dic_dir-user_dic-filter-persistent-static) method is static and so does not require an instance of Tagger. It might, however, need the dictionary directory path to be passed as an argument in order to function. 
```php
$split = \Mecab_Tagger::split('眠いです');
```
Or if that doesnt work.
```php
$split = \Mecab_Tagger::split('眠いです', '/path/to/dictionary/mecab/dic/ipadic-utf8');

print_r($split);

// Results
Array
(
    [0] => 眠い
    [1] => です
)

```

If you have an instance of MeCab\Tagger you can also call the method on the object.  You will still need to pass the dictionary directory.
```php
$split = $mecab->split('たこ焼きが食べたい');

print_r($split);

// Results
Array
(
    [0] => たこ焼き
    [1] => が
    [2] => 食べ
    [3] => たい
)

```

#### Functional
Use the funtion [mecab_split()](#mecab_splitstring-dic_dir-user_dic-filter-persistent).  It may or may not require the dictionary directory to be passed.
```php
$split = mecab_split('パンダをいくらで買いますか');
```
Or....
```php
$split = mecab_split('パンダをいくらで買いますか', '/path/to/dictionary/mecab/dic/ipadic-utf8');

print_r($split);

// Results
Array
(
    [0] => パンダ
    [1] => を
    [2] => いくら
    [3] => で
    [4] => 買い
    [5] => ます
    [6] => か
)
```

[Top](#contents)

### Parsing Strings
MeCab will parse strings of Japanese text and return results in either string form or as a MeCab\Node.  The MeCab\Node class seems a little awkward and difficult to deal with at first, but they give the user a lot of power and make parsing results a little easier.

#### Object Orientated
To parse a string and get results in string form, a couple options exist.  The first is the [parse()](#parsestring-length-output_length) method.
```php
$results = $mecab->parse('チョコレートがやめられない');

echo $results;

// Results
チョコレート    名詞,一般,*,*,*,*,チョコレート,チョコレート,チョコレート
が      助詞,格助詞,一般,*,*,*,が,ガ,ガ
やめ    動詞,自立,*,*,一段,未然形,やめる,ヤメ,ヤメ
られ    動詞,接尾,*,*,一段,未然形,られる,ラレ,ラレ
ない    助動詞,*,*,*,特殊・ナイ,基本形,ない,ナイ,ナイ
EOS
```

You could also use the [parseToString()](#parsetostringstring-length-output_length) method which produces the exact same results.
```php
$results = $mecab->parseToString('チョコレートがやめられない');

echo $results;

// Results
チョコレート    名詞,一般,*,*,*,*,チョコレート,チョコレート,チョコレート
が      助詞,格助詞,一般,*,*,*,が,ガ,ガ
やめ    動詞,自立,*,*,一段,未然形,やめる,ヤメ,ヤメ
られ    動詞,接尾,*,*,一段,未然形,られる,ラレ,ラレ
ない    助動詞,*,*,*,特殊・ナイ,基本形,ない,ナイ,ナイ
EOS
```

To get results in node form, use [parseToNode()](#parsetonodestring-length).
```php
$node = $mecab->parseToNode('ご飯作りたくない');

var_dump($node);

// Results
object(MeCab\Node) (0) {
}
```

#### Functional
To get results as a string, use the function [mecab_sparse_tostr()](#mecab_sparse_tostrmecab-string-length-output_length).
```php
$node = mecab_sparse_tostr($mecab, 'パンダいらないよね');

echo $node;

// Results
パンダ  名詞,一般,*,*,*,*,パンダ,パンダ,パンダ
いら    動詞,自立,*,*,五段・ラ行,未然形,いる,イラ,イラ
ない    助動詞,*,*,*,特殊・ナイ,基本形,ない,ナイ,ナイ
よ      助詞,終助詞,*,*,*,*,よ,ヨ,ヨ
ね      助詞,終助詞,*,*,*,*,ね,ネ,ネ
EOS
```

For node results, use [mecab_sparse_tonode()](#mecab_sparse_tonodemecab-string-length).
```php
$node = mecab_sparse_tonode($mecab, 'これ長くなってる');

var_dump($node);

// Results
resource(5) of type (node)
```

[Top](#contents)

###Using Nodes
Nodes make it easy to access the information MeCab provides and give users powerful ways to navigate through results.  
  
The node returned from the parseToNode() methods discussed in the previous section is the first node in the series and only represents the first morpheme.  In order to get information about the entire string, it is necessary to walk through all the nodes in the series.  But before we tackle that, lets take a quick look at some of more useful methods we have at our disposal.

#### Object Orientated
  - [getPrev()](#getprev): Get the previous node in the series.
  - [getNext()](#getnext): Get the next node in the series.
  - [getSurface()](#getsurface): Get the surface (the original morpheme) of the node.
  - [getFeature()](#getfeature): Get the feature (the MeCab info) of the node.
  - [getLength()](#getlength): Get the length of the node's surface.
  - [toArray()](#toarraydump_all): Get all the node's elements as an associative array.

#### Functional
  - [mecab_node_prev()](#mecab_node_prevnode): Get the previous node in the series.
  - [mecab_node_next()](#mecab_node_nextnode): Get the next node in the series.
  - [mecab_node_surface()](#mecab_node_surfacenode): Get the surface (the original morpheme) of the node.
  - [mecab_node_feature()](#mecab_node_featurenode): Get the feature (the MeCab info) of the node.
  - [mecab_node_length()](#mecab_node_lengthnode): Get the length of the node's surface.
  - [mecab_node_toarray()](#mecab_node_toarraynode-dump_all): Get all the node's elements as an associative array.

There are several other methods available, but these are the most useful at this point.  For a full list of methods, see the [Classes and Functions](#classes-and-functions) section of this guide.  
So let's see how we can walk through the nodes and extract the information we need.

#### Object Orientated
You can go about this a couple ways.
The first way simply walks through the nodes with a foreach loop.  
```php
$node = $mecab->parseToNode('カレーライスにしようかな');

foreach ($node as $n) {
    echo $n->getFeature() . "\n";
}

// Results
BOS/EOS,*,*,*,*,*,*,*,*
名詞,一般,*,*,*,*,カレーライス,カレーライス,カレーライス
助詞,格助詞,一般,*,*,*,に,ニ,ニ
動詞,自立,*,*,サ変・スル,未然ウ接続,する,シヨ,シヨ
助動詞,*,*,*,不変化型,基本形,う,ウ,ウ
助詞,副助詞／並立助詞／終助詞,*,*,*,*,か,カ,カ
助詞,終助詞,*,*,*,*,な,ナ,ナ
BOS/EOS,*,*,*,*,*,*,*,*
```
This isn't necessairly a bad way to do it, but it's a little too magical for my liking.  If $node is the first node in the series (and it is, you can var_dump and verify this), it doesn't make sense to loop through each $node as $n where $node is a single node and $n is also a single node.  Instead, I prefer to use MeCab\Node's methods to explicitly define what I am doing.
```php
$node = $mecab->parseToNode('これの方がいい');

do {
    echo $node->getFeature() . "\n";
} while ($node = $node->getNext());

// Results
BOS/EOS,*,*,*,*,*,*,*,*
名詞,代名詞,一般,*,*,*,これ,コレ,コレ
助詞,連体化,*,*,*,*,の,ノ,ノ
名詞,非自立,一般,*,*,*,方,ホウ,ホー
助詞,格助詞,一般,*,*,*,が,ガ,ガ
形容詞,自立,*,*,形容詞・イイ,基本形,いい,イイ,イイ
BOS/EOS,*,*,*,*,*,*,*,*
```
We can extract the logic to a general purpose looping function.

```php
function walkThroughNodes(\Mecab\Node $node, $callback)
{
    do {
        $callback($node);
    } while ($node = $node->getNext());
}
```
We can then pass our walkThroughNodes function a closure to tell it what to do with each node.
```php
$node = $mecab->parseToNode('これの方がいい');

walkThroughNodes($node, function($node) {
    echo $node->getSurface() . "\n";
});

// Results

これ
の
方
が
いい

```
Now we have never have to worry about a basic walkthough again.  We can simply pass our walkThroughNodes function a node and a callback.

#### Functional
As mentioned in the Object Orientated section above, we can simply walk through the nodes with a foreach loop, but I don't like that approach.  Instead, lets use MeCab's nodes to our advantage.
```php
$node = mecab_sparse_tonode($mecab, 'ビール飲みたい');

do {
    echo mecab_node_surface($node) . "\n";
} while ($node = mecab_node_next($node));

// Results

ビール
飲み
たい

```
Like we did in the Object Orientated section, lets extract this to a function that we can send a callback to.
```php
function walkThroughNodes($node, $callback)
{
    do {
        $callback($node);
    } while ($node = mecab_node_next($node));
}
```
We can cuse our walkThroughNodes function like this.
```php
$node = mecab_sparse_tonode($mecab, 'ビール飲みたい');

walkThroughNodes($node, function ($node) {
    echo mecab_node_surface($node) . "\n";
});

// Results

ビール
飲み
たい

```

### Basic MeCab
Now that we can extract information from Japanese strings using MeCab and php-mecab, let's take a quick look at what this information means. 
```php
$mecab = new \Mecab\Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

$string = $mecab->parseToString('行く');

echo $string;

// Results
行く    動詞,自立,*,*,五段・カ行促音便,基本形,行く,イク,イク
EOS
```
Commonly in MeCab you will see BOS and EOS.  These mean 'Beginning of Sentence' and 'End of Sentence', respectively.   
In output lines, there are generally two parts, the surface and the feature.  The surface is the original morpheme and the feature is MeCab info.  In our case, '行く' is the surface and '動詞,自立,*,*,五段・カ行促音便,基本形,行く,イク,イク' is the feature.  Remember you can use nodes to easily extract this information.  
  
The feature is a comma seperated string with nine sections.    
Section 1: Main part of speech category  
Section 2: Part of speech sub-category  
Section 3: Part of speech sub-category   
Section 4: Part of speech sub-category   
Section 5: Inflection type   
Section 6: Inflection form   
Section 7: Lemma (the root word found in the dictionary)   
Section 8: Reading   
Section 9: Pronunciation   

In our example:
```php
print_r(explode(',', '動詞,自立,*,*,五段・カ行促音便,基本形,行く,イク,イク'));

// Results
    [0] => 動詞  // Main part of speech category
    [1] => 自立  // Part of speech sub-category
    [2] => *  // Part of speech sub-category (none)
    [3] => *  // Part of speech sub-category (none)
    [4] => 五段・カ行促音便  // Inflection type
    [5] => 基本形  // Inflection form
    [6] => 行く  // Lemma (the root word found in the dictionary)
    [7] => イク  // Reading
    [8] => イク  // Pronunciation
```

What you do with this information is up to you!  

[Top](#contents)

## Classes and Functions  
### Classes
  - [MeCab\Tagger](#mecab\tagger)
  - [MeCab\Node](#mecab\node)
  - [Mecab\Path](#mecab\path)
  - [MeCab\NodeIterator](#mecab\nodeiterator)

#### MeCab\Tagger   
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
/**
 * @return      string
 */
```

###### split($string, $dic_dir, $user_dic, $filter, $persistent) [static]
Only on versions prior to 0.6.0.    
Split string into array of morphemes.  Usually requires the dictionary directory to be passed as a parameter.
```php
/**
 * @param       string          $string         String to split.  
 * @param       string          $dic_dir        Path to dictionary directory. (Optional)    
 * @param       string          $user_dic       Path to user dictionary. (Optional)   
 * @param       callback        $filter         Filter function or method. (Optional)      
 * @param       boolean         $persistent     (Optional)    
 *
 * @return      array 
 */
```
Example  
```php
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
```php
/**
 * @param       array           $arguments      Command line arguments.        
 * @param       boolean         $persistent     (Optional)    
 *
 * @return      MeCab\Tagger 
 */
```

###### getPartial()  
Get current partial parsing mode state.  
```php
/**
 * @return      boolean
 */
```

###### setPartial($bool)  
Set partial parsing mode.  
```php
/**
 * @param       boolean         $bool           Partial parsing mode.
 */
```

###### getTheta()  
Get current temparature parameter theta.  
```php
/**
 * @return      float
 */
```

###### setTheta($theta)  
Set temparature parameter theta.
```php
/**
 * @param       float/int       $theta          Temparature parameter theta.
 */
```

###### getLatticeLevel()  
Get current lattice level.
```php
/**
 * @return      int
 */
```

###### setLatticeLevel($level)  
Set lattice level.
```php
/**
 * @param       int             $level          Lattice level.
 */
```

###### getAllMorphs()  
Get all-morphs output mode.
```php
/**
 * @return      bool
 */
```

###### setAllMorphs($bool)  
Set all-morphs output mode.
```php
/**
 * @param       bool            $bool           All-morphs output mode.
 */
```

###### parse($string, $length, $output_length)  
Parse string and output results as string.
```php
/**
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)  
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```
Example
```php
$mecab = new \Mecab\Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

$string = $mecab->parse('行きます');

print_r($string);

行き    動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ
ます    助動詞,*,*,*,特殊・マス,基本形,ます,マス,マス
EOS
```

###### parseToString($string, $length, $output_length)  
Parse string and output results as string.
```php
/**
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)  
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```
Example
```php
$mecab = new \Mecab\Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

$string = $mecab->parseToString('行きます');

print_r($string);

行き    動詞,自立,*,*,五段・カ行促音便,連用形,行く,イキ,イキ
ます    助動詞,*,*,*,特殊・マス,基本形,ます,マス,マス
EOS
```

###### parseToNode($string, $length)  
Parse string and output results as MeCab/Node.
```php
/**
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)
 *
 * @return      MeCab/Node
 */
```
Example
```php
$mecab = new \Mecab\Tagger(['-d', '/var/lib/mecab/dic/ipadic-utf8']);

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
```php
/**
 * @param       int             $n              Number of results to obtain.
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```

###### parseNBestInit($string, $length)  
Initialize N-best enumeration with a sentence. 
```php
/**
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)

 * @return      boolean
 */
```

###### next($output_length)  
Get the next result of N-Best as a string.
```php
/**
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```

###### nextNode()  
Get the next result of N-Best as a node.
```php
/**
 * @return      MeCab\Node
 */
```

###### formatNode($node)  
Format a node to a string.
```php
/**
 * @param       MeCab\Node      $node           Node to be formatted.
 *
 * @return      string
 */
```

###### dictionaryInfo()
Return array of dictionary info.
```php
/**
 * @return      array
 */
```

[Top](#contents)

#### Mecab/Node
Returned by parseToNode method on Mecab\Tagger.
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
Return MeCab\NodeIterator.
```php
/**
 * @return      MeCab\NodeIterator
 */
```

###### setTraverse($mode)
Set the traverse mode.
```php
/**
 * @param       long            $mode           Traverse mode.
 */
```

###### getPrev()
Get the previous node.  Return NULL if none.
```php
/**
 * @return      MeCab\Node
 */
```

###### getNext()
Get the next node.  Return NULL if none.
```php
/**
 * @return      MeCab\Node
 */
```

###### getENext()
Get the next node which has same end point as the given node.  Return NULL if none.
```php
/**
 * @return      MeCab\Node
 */
```

###### getBNext()
Get the next node which has same beginning point as the given node.  Return NULL if none.
```php
/**
 * @return      MeCab\Node
 */
```

###### getRPath()
Get the next node which has same end point as the given node.  Return NULL if none.
```php
/**
 * @return      MeCab\Path
 */
```

###### getLPath()
Get the next node which has same beginning point as the given node.  Return NULL if none.
```php
/**
 * @return      MeCab\Path
 */
```

###### getSurface()
Get the surface of the node.
```php
/**
 * @return      string
 */
```

###### getFeature()  
Get the feature of the node.
```php
/**
 * @return      string
 */
```

###### getId()  
Get the ID of the node.
```php
/**
 * @return      int
 */
```

###### getLength()
Get the length of the node's surface.
```php
/**
 * @return      int
 */
```

###### getRLength()  
Get the length of the node's surface including it's leading whitespace.
```php
/**
 * @return      int
 */
```

###### getRcAttr()
Get the ID of the right context.
```php
/**
 * @return      int
 */
```

###### getLcAttr()
Get the ID of the left context.
```php
/**
 * @return      int
 */
```

###### getPosId()
Get the ID of the part of speech.
```php
/**
 * @return      int
 */
```

###### getCharType()
Get the type of character.
```php
/**
 * @return      int
 */
```

###### getStat()
Get the status of the node.
```php
/**
 * @return      int
 */
```
0: Normal, MECAB_NOR_NODE  
1: Unknown, MECAB_UNK_NODE  
2: Beginning of Sentence, MECAB_BOS_NODE  
3: End of Sentence, MECAB_EOS_NODE  

###### getAlpha()
Get the forward log probability.
```php
/**
 * @return      float
 */
```

###### getBeta()
Get the backward probability log.
```php
/**
 * @return      float
 */
```

###### getWCost()
Get the word arising cost.
```php
/**
 * @return      int
 */
```

###### getCost()
Get the cumulative cost of the node. 
```php
/**
 * @return      int
 */
```

###### getProb()
Get the marginal probability of the node.
```php
/**
 * @return      float
 */
```

###### isBest()
Determine whether the node is the best solution.
```php
/**
 * @return      boolean
 */
```

###### toArray($dump_all)
Get all elements of the node as an associative array.
```php
/**
 * @param       boolean         $dump_all       Dump all related nodes if true. (Optional)
 *
 * @return      array
 */
```

###### toString()
Get the formatted string of the node.
```php
/**
 * @return      string
 */
```

[Top](#contents)

#### MeCab\Path
Returned by getRPath and getLPath methods on MeCab/Node class.
##### Methods
 - [getRNext()](#getrnext)
 - [getLNext()](#getlnext)
 - [getRNode()](#getrnode)
 - [getLNode()](#getlnode)
 - [getProb()](#getprob-1)
 - [getCost()](#getcost-1)

###### getRNext()
Get the rnext path. Return NULL if none.
```php
/**
 * @return      MeCab/Path
 */
```

###### getLNext()
Get the lext path. Return NULL if none.
```php
/**
 * @return      MeCab/Path
 */
```

###### getRNode()
Get the rnode. Return NULL if none.
```php
/**
 * @return      MeCab/Node
 */
```

###### getLNode()
Get the lnode. Return NULL if none.
```php
/**
 * @return      MeCab/Node
 */
```

###### getProb()
Get the marginal probability of the path.
```php
/**
 * @return      float
 */
```

###### getCost()
Get the cumulative cost of the path. 
```php
/**
 * @return      int
 */
```

[Top](#contents)

#### MeCab\NodeIterator
Node iterator class.
##### Methods
  - [current()](#current)
  - [key()](#key)
  - [next()](#next)
  - [rewind()](#rewind)
  - [valid()](#valid)

###### current()
Return the current element.
```php
/**
 * @return      MeCab\Node
 */
```

###### key()
```php
/**
 * @return      int
 */
```

###### next()
Set pointer to next element.

###### rewind()
Set pointer to beginning.

###### valid()
Check if there is a current element after calls to rewind() or next().
```php
/**
 * @return   boolean
 */
```

[Top](#contents)

### Functions
  - [mecab_version()](#mecab_version)
  - [mecab_split()](#mecab_splitstring-dic_dir-user_dic-filter-persistent)
  - [mecab_new()](#mecab_newarguments-persistent)
  - [mecab_destroy()](#mecab_destroymecab)
  - [mecab_get_partial](#mecab_get_partialmecab)
  - [mecab_set_partial()](#mecab_set_partialmecab-partial)
  - [mecab_get_theta()](#mecab_get_thetamecab)
  - [mecab_set_theta()](#mecab_set_thetamecab-theta)
  - [mecab_get_lattice_level()](#mecab_get_lattice_levelmecab)
  - [mecab_set_lattice_level()](#mecab_set_lattice_levelmecab-level)
  - [mecab_get_all_morphs()](#mecab_get_all_morphsmecab)
  - [mecab_set_all_morphs()](#mecab_set_all_morphsmecab-bool)
  - [mecab_sparse_tostr()](#mecab_sparse_tostrmecab-string-length-output_length)
  - [mecab_sparse_tonode()](#mecab_sparse_tonodemecab-string-length)
  - [mecab_nbest_sparse_tostr()](#mecab_nbest_sparse_tostrmecab-n-string-length-output_length)
  - [mecab_nbest_init()](#mecab_nbest_initmecab-string-length)
  - [mecab_nbest_next_tostr()](#mecab_nbest_next_tostrmecab-output_length)
  - [mecab_nbest_next_tonode()](#mecab_nbest_next_tonodemecab)
  - [mecab_format_node()](#mecab_format_nodemecab-node)
  - [mecab_dictionary_info()](#mecab_dictionary_infomecab)
  - [mecab_node_toarray()](#mecab_node_toarraynode-dump_all)
  - [mecab_node_tostring()](#mecab_node_tostringnode)
  - [mecab_node_prev()](#mecab_node_prevnode)
  - [mecab_node_next()](#mecab_node_nextnode)
  - [mecab_node_enext()](#mecab_node_enextnode)
  - [mecab_node_bnext()](#mecab_node_bnextnode)
  - [mecab_node_rpath()](#mecab_node_rpathnode)
  - [mecab_node_lpath()](#mecab_node_lpathnode)
  - [mecab_node_surface()](#mecab_node_surfacenode)
  - [mecab_node_feature()](#mecab_node_featurenode)
  - [mecab_node_id()](#mecab_node_idnode)
  - [mecab_node_length()](#mecab_node_lengthnode)
  - [mecab_node_rlength()](#mecab_node_rlengthnode)
  - [mecab_node_rcattr()](#mecab_node_rcattrnode)
  - [mecab_node_lcattr()](#mecab_node_lcattrnode)
  - [mecab_node_posid()](#mecab_node_posidnode)
  - [mecab_node_char_type()](#mecab_node_char_typenode)
  - [mecab_node_stat()](#mecab_node_statnode)
  - [mecab_node_alpha()](#mecab_node_alphanode)
  - [mecab_node_beta()](#mecab_node_betanode)
  - [mecab_node_wcost()](#mecab_node_wcostnode)
  - [mecab_node_cost()](#mecab_node_costnode)
  - [mecab_node_prob()](#mecab_node_probnode)
  - [mecab_node_isbest()](#mecab_node_isbestnode)
  - [mecab_path_rnext()](#mecab_path_rnextpath)
  - [mecab_path_lnext()](#mecab_path_lnextpath)
  - [mecab_path_rnode()](#mecab_path_rnodepath)
  - [mecab_path_lnode()](#mecab_path_lnodepath)
  - [mecab_path_prob()](#mecab_path_probpath)
  - [mecab_path_cost()](#mecab_path_costpath)  

###### mecab_version()
Return MeCab version.
Return MeCab version.
```php
/**
 * @return      string
 */
```

###### mecab_split($string, $dic_dir, $user_dic, $filter, $persistent)
Split string into array of morphemes.
```php
/**
 * @param       string          $string         String to split.  
 * @param       string          $dic_dir        Path to dictionary directory. (Optional)    
 * @param       string          $user_dic       Path to user dictionary. (Optional)   
 * @param       callback        $filter         Filter function or method. (Optional)      
 * @param       boolean         $persistent     (Optional)    
 *
 * @return      array
 */
```

###### mecab_new($arguments, $persistent)
Create new MeCab resource.
```php
/**
 * @param       array           $arguments      Command line arguments.        
 * @param       boolean         $persistent     (Optional)    
 *
 * @return      MeCab
 */
```

###### mecab_destroy($mecab)
Free the tagger.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 */
```

###### mecab_get_partial($mecab)
Get current partial parsing mode state.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 *
 * @return      boolean
 */
```

###### mecab_set_partial($mecab, $partial)
Set partial parsing mode.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       boolean         $bool           Partial parsing mode.
 */
```

###### mecab_get_theta($mecab)
Get current temparature parameter theta. 
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 *
 * @return      float
 */
```

###### mecab_set_theta($mecab, $theta)
Set temparature parameter theta.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       float/int       $theta          Temparature parameter theta.
 */
```

###### mecab_get_lattice_level($mecab)
Get current lattice level.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 *
 * @return      int
 */
```

###### mecab_set_lattice_level($mecab, $level)
Set lattice level.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       int             $level          Lattice level.
 */
```

###### mecab_get_all_morphs($mecab)
Get all-morphs output mode.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 *
 * @return      bool
 */
```

###### mecab_set_all_morphs($mecab, $bool)
Set all-morphs output mode.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       bool            $bool           All-morphs output mode.
 */
```

###### mecab_sparse_tostr($mecab, $string, $length, $output_length)
Parse string and output results as string.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)  
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```

###### mecab_sparse_tonode($mecab, $string, $length)
Parse string and output results as MeCab/Node.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)
 *
 * @return      MeCab/Node
 */
```

###### mecab_nbest_sparse_tostr($mecab, $n, $string, $length, $output_length)
Parse given sentence and output N-best results as string. This method causes seg faults for me.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       int             $n              Number of results to obtain.
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```

###### mecab_nbest_init($mecab, $string, $length)
Initialize N-best enumeration with a sentence. 
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       string          $string         String to be parsed.
 * @param       int             $length         Length to be analyzed. (Optional)

 * @return      boolean
 */
```

###### mecab_nbest_next_tostr($mecab, $output_length)
Get the next result of N-Best as a string.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       int             $output_length  Maximum length of output. (Optional)
 *
 * @return      string
 */
```

###### mecab_nbest_next_tonode($mecab)
Get the next result of N-Best as a node.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 *
 * @return      MeCab\Node
 */
```

###### mecab_format_node($mecab, $node)
Format a node to a string.
```php
/**
 * @param       MeCab           $mecab          MeCab resource.
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      string
 */
```

###### mecab_dictionary_info($mecab)
Return array of dictionary info.
```php
/**
 * @return      array
 */
```

###### mecab_node_toarray($node, $dump_all)
Get all elements of the node as an associative array.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 * @param       boolean         $dump_all       Dump all related nodes if true. (Optional)
 *
 * @return      array
 */
```

###### mecab_node_tostring($node)
Get the formatted string of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      string
 */
```

###### mecab_node_prev($node)
Get the previous node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_node_next($node)
Get the next node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_node_enext($node)
Get the next node which has same end point as the given node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_node_bnext($node)
Get the next node which has same beginning point as the given node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_node_rpath($node)
Get the next node which has same end point as the given node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Path
 */
```

###### mecab_node_lpath($node)
Get the next node which has same beginning point as the given node. Return NULL if none.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      MeCab\Path
 */
```

###### mecab_node_surface($node)
Get the surface of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      string
 */
```

###### mecab_node_feature($node)
Get the feature of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      string
 */
```

###### mecab_node_id($node)
Get the ID of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_length($node)
Get the length of the node's surface.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_rlength($node)
Get the length of the node's surface including it's leading whitespace.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_rcattr($node)
Get the ID of the right context.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_lcattr($node)
Get the ID of the left context.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_posid($node)
Get the ID of the part of speech.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_char_type($node)
Get the type of character.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_stat($node)
Get the status of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```
0: Normal, MECAB_NOR_NODE   
1: Unknown, MECAB_UNK_NODE   
2: Beginning of Sentence, MECAB_BOS_NODE   
3: End of Sentence, MECAB_EOS_NODE    

###### mecab_node_alpha($node)
Get the forward log probability.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      float
 */
```

###### mecab_node_beta($node)
Get the backward probability log.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      float
 */
```

###### mecab_node_wcost($node)
Get the word arising cost.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_cost($node)
Get the cumulative cost of the node. 
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      int
 */
```

###### mecab_node_prob($node)
Get the marginal probability of the node.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 *
 * @return      float
 */
```

###### mecab_node_isbest($node)
Determine whether the node is the best solution.
```php
/**
 * @param       MeCab\Node      $node           Node of source string.
 * 
 * @return      boolean
 */
```

###### mecab_path_rnext($path)
Get the rnext path. Return NULL if none.
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      MeCab\Path
 */
```

###### mecab_path_lnext($path)
Get the lext path. Return NULL if none.
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      MeCab\Path
 */
```

###### mecab_path_rnode($path)
Get the rnode. Return NULL if none.
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_path_lnode($path)
Get the lnode. Return NULL if none.
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      MeCab\Node
 */
```

###### mecab_path_prob($path)
Get the marginal probability of the path.
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      float
 */
```

###### mecab_path_cost($path)
Get the cumulative cost of the path. 
```php
/**
 * @param       MeCab\Path      $path           Path of source string.
 *
 * @return      int
 */
```

[Top](#contents)

## Other Resources
The University of the Ryukyus Department of Mechanical Systems Engineering maintains a php-mecab API documentation page that can be useful.   
[http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html](http://mechsys.tec.u-ryukyu.ac.jp/~oshiro/php_mecab_apis.html)    
  
The MeCab documentation is here on github, but its in Japanese only and is a little outdated.   
[http://taku910.github.io/mecab/](http://taku910.github.io/mecab/)    

jordwest has translated parts the MeCab documentation into English here.   
[https://github.com/jordwest/mecab-docs-en](https://github.com/jordwest/mecab-docs-en)
  
The MeCab api documentation is up on googlecode.  
[https://mecab.googlecode.com/svn/trunk/mecab/doc/doxygen/index.html](https://mecab.googlecode.com/svn/trunk/mecab/doc/doxygen/index.html)
  
If you're using an IDE, fumikito has a gist that can help with php-mecab class recognition.  
[https://gist.github.com/fumikito/bb172b4cf5648c7f8451](https://gist.github.com/fumikito/bb172b4cf5648c7f8451)  
  
If an app your using requires php-mecab and you'd like to use Travis CI, check out the [example-travis.yml](https://github.com/nihongodera/php-mecab-documentation/blob/master/example-travis.yml) file and the accompanying [travis-install-php.sh](https://github.com/nihongodera/php-mecab-documentation/blob/master/travis-install-php-mecab.sh) file in this repository.

[Top](#contents)

## Contributing
Please help me to improve this guide.  If you find errors or places where you feel this guide is lacking, please create an issue or make a pull request.  Also, I would love to see this guide translated into other languages, especially Japanese.  Any help with translations would be much appreciated.
