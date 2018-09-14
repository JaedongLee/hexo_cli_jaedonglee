---
title: Python_Comprehend_Daily
date: 2017-12-21 11:28:01
tags:
categroy: Python
---
- argparse
    - add_argument
        - const/default: these two attribute can not only be const like `1` or `one`, but can be function like `max`/`sum`/`all`<!-- more -->
- curses
    - getch(): window.getch()/stdscr.getch()
- list/tuple
    - tuple:
        ```
        tuple1 = ([1, 2, 3],(4, 5, 6), (7, 8, 9))
        tuple2 = ((1, 2, 3),(4, 5, 6), (7, 8, 9))
        type(tuple1[0]) = list
        type(tuple1[1]) = tuple
        type(list(tuple1[1])) = list
        ```
- defaultdict
    - source: collections
    - notes
        - TypeError: first argument must be callable or None
            - For a `defaultdict`, the default value is usually not really a value, it is a factory: a method that generates a new value.
            - Example:
                - Wrong:
                    ```
                    lst = list(range(0, 5))
                    d = defaultdict(lst)
                    ```
                    the terminal will report an error like this:
                    ```
                    TypeError: first argument must be callable or None
                    ```
                - Right:
                    ```
                    d = defaultdict(lambda: list(range(0, 5)))
                    ```
            - Extend: the codes below will make all default values(like d[1]/d[2]) reference the same list
                - Example:
                    - Wrong:
                        ```
                        >>> val = list(range(0, 5))
                        >>> d = defaultdict(lambda: val)
                        >>> d[1]
                        [0, 1, 2, 3, 4]
                        >>> d[1].append(14)
                        >>> d[2]
                        [0, 1, 2, 3, 4, 14]
                        ```
                    - Right:
                        ```
                        >>> val = lambda: list(range(0, 5))
                        >>> d = defaultdict(val)
                        >>> d[1]
                        [0, 1, 2, 3, 4]
                        >>> d[1].append(14)
                        >>> d[2]
                        [0, 1, 2, 3, 4]
                        ```
                - Reason: 