# Tips and Tricks for Writing Scientific Papers

## Table of contents

  * [Tips and Tricks for Writing Scientific Papers](#tips-and-tricks-for-writing-scientific-papers)
    * [Table of contents](#table-of-contents)
    * [What is this?](#what-is-this)
  * [Typesetting your paper](#typesetting-your-paper)
    * [Capitalization](#capitalization)
    * [Tables](#tables)
    * [Number formatting](#number-formatting)
  * [Bibliography](#bibliography)
    * [Back references](#back-references)
  * [Creating figures](#creating-figures)
    * [One script per data\-driven figure](#one-script-per-data-driven-figure)
    * [Python helper script](#python-helper-script)
    * [Figures format](#figures-format)
    * [Rasterize parts of the figure](#rasterize-parts-of-the-figure)
  * [Useful resources](#useful-resources)

## What is this?

This repository contains a list of tools, best practices, tips and other guidelines we found useful/important when writing scientific papers.
Some are a matter of style (we tend to follow the guidelines of the Chicago Manual of Style), and we are well aware that other people prefer to do things differently, but we list them anyway to have a consistent guide.
Feel free to adapt, change, ignore, or even challenge everything we write!

# Typesetting your paper

Typesetting is the composition of text by means of arranging the types, i.e., letters and symbols.
It is mostly a question of a aesthetics, but beautiful typography makes documents easier and more pleasant to read, helping the reader to get to the message.

We list below some typesetting tips and tools to help you when composing your documents.
Some tips are specific to LaTeX, but others apply regardless of what you are using.

## Capitalization

We will refer below to two types of capitalization:
* sentence format : The title of the nice book
* title format: The Title of the Nice Book

Use title format for all section, subsection, etc. titles.

## Tables

[booktabs](https://www.ctan.org/pkg/booktabs) can help you produce clean and nice-looking tables.

```
\usepackage{booktabs}
% --
\begin{table}
	\centering
	\begin{tabular}{lcc}
		\toprule
		& \multicolumn{2}{c}{Data} \\ \cmidrule(lr){2-3}
		Name & Column 1 & Another column \\
		\midrule
		Some data & 10 & 95 \\
		Other data & 30 & 49 \\
		\addlinespace
		Different stuff & 99 & 12 \\
		\bottomrule
	\end{tabular}
	\caption{My caption.}
	\label{tab-label}
\end{table}
```

![Booktabs example](https://github.com/Wookai/paper-tips-and-tricks/raw/master/examples/booktabs/booktabs.png)

In general, avoid using vertical lines in your tables.
Instead, if you want to group columns, do it in the headers using `\cmidrule`.
You can also replace horizontal lines with spacing, using `\addlinespace`.

Column heads should use sentence-format capitalization (see http://www.chicagomanualofstyle.org/15/ch13/ch13_sec019.html).

You can find more advice on table formatting here: http://www.inf.ethz.ch/personal/markusp/teaching/guides/guide-tables.pdf
Here is a nice GIF that illustrates some of these rules:

![Better table formatting](http://darkhorseanalytics.com/blog/wp-content/uploads/2014/03/ClearOffTheTableMd.gif)

## Number formatting

Use the [siunitx](https://ctan.org/pkg/siunitx) package to format all numbers, currencies, units, etc:
```
\usepackage{siunitx}
% ---
This thing costs \SI{123456}{\$}.
There are \num{987654} people in this room, \SI{38}{\percent} of which are male.
```

![Siunitx formatting](https://github.com/Wookai/paper-tips-and-tricks/raw/master/examples/siunitx/siunitx-formatting.png)

You can also use it to round numbers:
```
\usepackage{siunitx}
% ---
\sisetup{
	round-mode = places,
	round-precision = 3
}%
You can also round numbers, for example \num{1.23456}.
```
![Siunitx formatting](https://github.com/Wookai/paper-tips-and-tricks/raw/master/examples/siunitx/siunitx-rounding.png)

Finally, it can help you better align numbers in a table:
```
\usepackage{booktabs}
\usepackage{siunitx}
%---
\begin{table}
	\centering
	\begin{tabular}{lS}
		\toprule
		Name & {Value} \\ % headers of S columns have to be in {}
		\midrule
		Test & 2.3456 \\
		Blah & 34.2345 \\
		Foo & -6.7835 \\
		Bar & 5642.5 \\
		\bottomrule
	\end{tabular}
	\caption{Numbers alignment with \texttt{siunitx}.}
\end{table}
```
![Siunitx formatting](https://github.com/Wookai/paper-tips-and-tricks/raw/master/examples/siunitx/siunitx-table.png)


# Bibliography

## Back references

For longer documents, such as a master or PhD thesis, it can be useful to have back references in the bibliography, to show where a reference was cited.
To do so, simply add the option `backref=page` to the `hyperref` package:

```
\usepackage[backref=page]{hyperref}
```

You can customize the way the back references appear with the following commands:

```
\renewcommand*{\backref}[1]{}
\renewcommand*{\backrefalt}[4]{{\footnotesize [%
    \ifcase #1 Not cited.%
	\or Cited on page~#2%
	\else Cited on pages #2%
	\fi%
]}}
```

![Backref custom appearance](https://github.com/Wookai/paper-tips-and-tricks/raw/master/examples/backref/backref.png)

# Creating figures

Figures are a very important part of a majority of papers: they are your way of communicating your results.
You should always think about what you are trying to say with each figure, and make sure that there is just enough information to support your message, not more.
For example, if you want to show patterns in 2d points (there are two clusters well separated), maybe it is not necessary to put ticks and values on the axes (the scale does not really matter)?
Figures should also not be too complex: it is better to have several figures conveying each one or two messages (method A is better than B, but converges slower) than one big messy figure.

## One script per data-driven figure

Some figures are hand-made, e.g., to explain a system or give a global picture, whereas others are data-driven, i.e., illustrae some data.
These data-driven figures should be scripted as much as possible: ideally, if your data changes, you should only have to run a script once to update your figure, without any other intervention (setting the view, zooming, saving/cropping the figure, etc).
Similarly, if the data required to generate a figure takes more than seconds to be produced, you should have a first script that computes and saves the data, and a second script that plots it.
This way, you will save a lot of time when working on the plot: you won't have to wait after each small change to the figure to see its effect.

We also recommend to save the command used to generate a figure in the LaTeX file, for example as a comment above the figure, especially if the script requires arguments.

```
\documentclass{article}

\usepackage{graphicx}

\begin{document}

% python figure_example.py --save ../../examples/figure/figure.eps
\begin{figure}
	\centering
	\includegraphics{figure.eps}
	\caption{Example of a sigmoid function}
\end{figure}

\end{document}
```

## Python helper script

If possible, all figures should use the same fonts for their labels, axes, etc.
In particular, you should not have one figure with big labels/ticks, and another with smaller ones.
One solution to achieve this is to define the size of your figure in the script that generates it, and not rescale it in your document (e.g., do not change set the width of the figure to `\textwidth` in your LaTeX document).

To have consistent figures, we recommend using a helper script, similar to our [`plot_utils.py`](https://github.com/Wookai/paper-tips-and-tricks/blob/master/src/pyton/plot_utils.py).
Using this script, you simply have to call the `figure_setup()` function to define all the sizes, then create a figure of the size you want, and save it.

```
import argparse
import matplotlib.pyplot as plt
import numpy as np
import plot_utils as pu


def main(args):
    x = np.linspace(-6, 6, 200)
    y = 1/(1 + np.exp(-x))

    pu.figure_setup()

    fig_size = pu.get_fig_size(10, 5)
    fig = plt.figure(figsize=fig_size)
    ax = fig.add_subplot(111)

    ax.plot(x, y, c='b', lw=pu.plot_lw())

    ax.set_xlabel('$x$')
    ax.set_ylabel('$\\sigma(x)$')

    ax.set_axisbelow(True)

    plt.grid()
    plt.tight_layout()

    if args.save:
        pu.save_fig(fig, args.save)
    else:
        plt.show()


if __name__ == '__main__':
    parser = argparse.ArgumentParser()

    parser.add_argument('-s', '--save')

    args = parser.parse_args()
    main(args)
```

## Figures format

We recommend saving all figures in the `EPS` format.
This way, you can use both `latex` and `pdflatex` to generate your documents, and enjoy beautiful vector graphics and texts.

## Rasterize parts of the figure

If you have many data points in your plot, the resulting EPS file might be very big.
You could save your figure as a PNG file, but this would result in blurry texts.
The solution is to rasterize parts of your figure, i.e., to tell `matplotlib` that the data points have to be rendered as a bitmap in the EPS file, while the rests is in vector format.

You can pass the `rasterized=True` keyword to most plotting fuctions in `matplotlib`.
You can also tell use different layers using the `zorder` and tell `matplotlib` to rasterize all the layers below a given `zorder` using the `set_rasterization_zorder()` method of the axis.
See [figure_rasterized_example.py](https://github.com/Wookai/paper-tips-and-tricks/blob/master/src/pyton/figure_rasterized_example.py) and http://matplotlib.org/examples/misc/rasterization_demo.html for examples of rasterization.

# Useful resources

* Automatically capitalize your title: http://titlecapitalization.com
* Chicago Manual of Style: http://www.chicagomanualofstyle.org
* Command-line check of weasel words, passive, etc: https://github.com/devd/Academic-Writing-Check
