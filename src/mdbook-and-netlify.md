# Personal Website with mdBook and Netlify 

04/11/2023

---

I think it is only fitting if I write this as the first article on this site.

Prior to this I already have a personal website that I also host using [Netlify](https://www.netlify.com/) but using a different template that I forked from [bchiang/v4](https://github.com/bchiang7/v4).

The template looks nice and all but a bit too complex for my taste. Now I want something simpler and lightweight. I promise you it has nothing to do with the fact that I could not figure out how to change the animation that shows up when you first open the site.

Anyway let's talk about how I set up this new personal website.

## Installing mdBook

There are various ways to do this, but I chose to build it from source because I already have [cargo](https://doc.rust-lang.org/cargo/) installed and I was a bit curious of how long it would take to build a rust project. 

To do that, you can run 
```sh
cargo install mdbook
```

If you want to install it using other method, you can follow the installation manual from the [docs](https://rust-lang.github.io/mdBook/guide/installation.html).

## Creating an mdBook Project

We can run this command to initialize an mdBook project
```sh
mdbook init [your-project-name]
```

mdBook will create a directory with this following files for you

```sh
├── book.toml
└── src
    ├── chapter_1.md
    └── SUMMARY.md

```

You can run this command to serve your project on local machine 
```sh
mdbook serve
```

The next step for me is to make some adjustments to `book.toml`.

I didn't want the print icon on the top right so I added this line
```toml
[output.html.print]
enable = false
```

But now the top right corner feels a bit empty, so it might be a good idea to put a link to this site's repo there.
```toml
[output.html]
...
git-repository-url = "https://github.com/aryuuu/book-of-me"

```

This is the current content of `book.toml` that I have

```toml
[book]
authors = ["M Algah Fattah Illahi"]
language = "en"
multilingual = false
src = "src"
title = "aryuuu.com"

[output.html]
default-theme = "navy"
git-repository-url = "https://github.com/aryuuu/book-of-me"

[output.html.print]
enable = false
```

Okay, now I guess it is time to do something that actually matters--writing some articles.

## Deploying Using Netlify

Sweet, now I need to make this site accessible for my readers, all 2 of them.

The first option that came to mind is [Netlify](https://www.netlify.com/), I have used it in the past and there is a free tier, so I created new project in Netlify and connect it to this repo.

We need to build this project first before deploying it and for that we need `mdbook` to be installed on Netlify's CI.

Conveniently mdBook provided pre-built binaries for each of their release, so we can just download that.

I added this as the build command for my Netlify project, adjust the binary version to suit your need.

```sh
curl -L https://github.com/rust-lang/mdBook/releases/download/v0.4.25/mdbook-v0.4.25-x86_64-unknown-linux-gnu.tar.gz | tar xvz && ./mdbook build 
```

I also need to set the `publish directory` to `book` or whatever you configure the output directory to be.

Now the deployment will automatically triggered when you push your changes to your repository.

### References

- [mdBook docs](https://rust-lang.github.io/mdBook/index.html)
- [Using mdBook in Netlify](https://github.com/rust-lang/mdBook/issues/1191#issuecomment-614049886)
