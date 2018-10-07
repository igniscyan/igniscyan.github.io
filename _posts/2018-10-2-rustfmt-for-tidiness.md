---
layout: post
title: ! "rustfmt: the solution to differing opinions"
image: /img/term.png
tags: [rust]
---

A common issue I've run into while working on school projects and side projects is that even though we take the time to set up version control through Github, we end up wasting time trying to sift through eachothers code which is almost always written with small differences. Even though we can use language specific naming principles, what about the smaller things like tabs vs. spaces, and brick loops vs block loops? This is where something like *rustfmt* comes in. Rather than forcing people to stick to one format while working on their own time, rustfmt allows you to reformat code to project specifications immediately after a push (with some setup, we'll get into that in just a bit). You can specify exactly what you want rustfmt to do through the `rustfmt.toml` file. The options you have for configuration are seemingly endless, and you can keep it as minimal as you want. If you're interested, our extremely minimal config will be included at the end.   

For a recent website backend project I've been working on, we decided this was the best route to take without forcing people to conciously make the effort to adhere to our style guidelines. For our project, we decided to go with indents being reformatted to 1 tab. I know, I know, Rust-Lang specifically states four spaces is the norm for style reasons, but it's an argument I've never been able to wrap my head around. No matter where you fall on the issue, the good news is that with rustfmt, you don't really have to worry about it either way. Regardless of how you format your code for Github, upon push you'll see it magically becomes inline with the standards employed by the project. The usefulness to this is two-fold:
1. You don't waste time with keeping project specific paradigms in mind while you write your code. Just code as you normally would, and rustfmt will do all the hard work for you. 
2. You speed up the time it takes to work on code you've never seen before. Even if the standards aren't exactly what you're used to, you know what to *expect*, making the process infinitely easier. 

By default, rustfmt is a cargo package that you run in the root directory of a project to have everything formatted all at once. While this is useful, we wanted to make it even more fool proof and ensure that no matter WHAT you push to the remote, it'd always be formatted. To do this, we had to use [Travis CI](https://travis-ci.org). Now normally, you use Travis to ensure that all code that is pushed and merged still results in a specific branch being compileable, which is something we employ regardless. But, because of just how versatile Travis is, you can just as easily include specific crates to execute on code after compilation test. Directly from rustfmt's GitHub page, here is what a minimal Travis CI config looks like:

```yml
language: rust
before_script:
- rustup component add rustfmt-preview
script:
- cargo fmt --all -- --check
- cargo build
- cargo test
```

This script adds the rustfmt package, runs it, and makes sure the code still compiles all at the same time. All the results from TravisCI go directly to your account page on the Travis Website,or if you want to have logs stored to a different repository all together you can set that up as well. Once this project gets a little further along, this tool is going to be *priceless*, as we'll be saving ourselves a lot of headache both reading through and compiling code, after about a half hour of set up for both of these tools.  

As promised, here is our project's config file: 

```yml
# Hard tabs forces tabs for indents, and spaces for formatting.
hard_tabs = true

# Sets maximum line width, default is 100 but just keeping this in for clarity.
max_width = 100

# Blank lines upper bound sets the maximum amount of blank lines between lines. If it goes over,
#empty lines will be trimmed until it's at the upper bound
blank_lines_upper_bound = 2

# Ignore function for ignoring specific files within the directory. To add a file to ignore,
#simply add its path and file name in quotations between the brackets. Useful for non .rs files,
#or files you just don't want to have formatted for reasons that have yet to be determined.  
#example: "src/types.rs"
ignore = [
]
```

