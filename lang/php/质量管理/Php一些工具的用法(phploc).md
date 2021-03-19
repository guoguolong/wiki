## phploc

https://github.com/sebastianbergmann/phploc

phploc是一个快速度量项目尺寸和项目结构的工具，以下是通过composer安装.

> composer global require 'phploc/phploc=*'

二进制程序被安装到 /.composer/vendor/bin目录，然后将其加入该目录到path（/.bash_profile）

> phploc /my-project-folder

返回类似如下报告

```
phploc 3.0.1 by Sebastian Bergmann.
Directories                                          4
Files                                               13
Size
  Lines of Code (LOC)                             1187
  Comment Lines of Code (CLOC)                     185 (15.59%)
  Non-Comment Lines of Code (NCLOC)               1002 (84.41%)
  Logical Lines of Code (LLOC)                     458 (38.58%)
    Classes                                        387 (84.50%)
      Average Class Length                          29
        Minimum Class Length                         5
        Maximum Class Length                       117
      Average Method Length                          7
        Minimum Method Length                        1
        Maximum Method Length                       52
    Functions                                        0 (0.00%)
      Average Function Length                        0
    Not in classes or functions                     71 (15.50%)

Cyclomatic Complexity
  Average Complexity per LLOC                     0.25
  Average Complexity per Class                    9.69
    Minimum Class Complexity                      1.00
    Maximum Class Complexity                     26.00
  Average Complexity per Method                   3.31
    Minimum Method Complexity                     1.00
    Maximum Method Complexity                    23.00

Dependencies
  Global Accesses                                    0
    Global Constants                                 0 (0.00%)
    Global Variables                                 0 (0.00%)
    Super-Global Variables                           0 (0.00%)
  Attribute Accesses                                86
    Non-Static                                      84 (97.67%)
    Static                                           2 (2.33%)
  Method Calls                                     249
    Non-Static                                     228 (91.57%)
    Static                                          21 (8.43%)

Structure
  Namespaces                                         5
  Interfaces                                         0
  Traits                                             0
  Classes                                           13
    Abstract Classes                                 0 (0.00%)
    Concrete Classes                                13 (100.00%)
  Methods                                           49
    Scope
      Non-Static Methods                            47 (95.92%)
      Static Methods                                 2 (4.08%)
    Visibility
      Public Methods                                31 (63.27%)
      Non-Public Methods                            18 (36.73%)
  Functions                                          0
    Named Functions                                  0 (0.00%)
    Anonymous Functions                              0 (0.00%)
  Constants                                          0
    Global Constants                                 0 (0.00%)
    Class Constants                                  0 (0.00%)
```