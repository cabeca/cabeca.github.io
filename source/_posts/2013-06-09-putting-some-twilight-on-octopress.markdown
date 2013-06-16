---
layout: post
title: "Putting some twilight on octopress"
date: 2013-06-09 18:13
comments: true
categories: 
---

So, this blog runs on [Octopress](http://octopress.org) which is great, but the default solarized syntax highlighting is... how to put this gently... colorful in a non-pleasant way. This is how code looks with the default theme:

![Octopress Solarized Syntax Highlighting](/images/octopress_solarized_syntax_highlighting.png)

So I decided to change that to my prefered Twilight theme from Sublime Text 2:

```ruby
class Float
  def number_decimal_places
    self.to_s.length-2
  end
  def to_fraction
    higher = 10**self.number_decimal_places
    lower = self*higher
    gcden = greatest_common_divisor(higher, lower)

    return (lower/gcden).round, (higher/gcden).round
  end

private
  def greatest_common_divisor(a, b)
     while a%b != 0
       a,b = b.round,(a%b).round
     end
     return b
  end
end
```

Phew! Bland and pastel colors > Bright and screaming colors. Much better!

There is a [blog post from Alejandra Estanislao](http://blog.alestanis.com/2013/02/04/octopress-and-the-twilight-color-scheme/) on how to do this but it goes a little too far, replacing the syntax highlighting engine of octopress from Pygments to CodeRay. I didn't want to do that (Pygments is a fine syntax highlighter) so, I digged a little deeper and found this [twilight_pigments.css gist by Dan Simpson](https://gist.github.com/dansimpson/803005).

With this in hand I've set out to discover where to put it inside octopress. The pygments syntax highlighting css rules are buried deep inside `sass/partials/_syntax.scss` nested in a `.pre-code` class. You can put custom scss in `sass/custom/_styles.scss` as this file will be imported last, and will override other styles in the cascade.

So I've adapted the twilight pigmets css to scss, changed `.pre.code` to `.pre-code`, added `!important` everywhere, changed some colors using Sublime Text 2 as a reference and ended up with this:

```sass
// This File is imported last, and will override other styles in the cascade
// Add styles here to make changes without digging in too much

.pre-code {
  font-family: Consolas, Monaco,"Lucida Console";
  overflow: scroll;
  overflow-y: hidden;
  display: block;
  padding: .8em;
  overflow-x: auto;
  line-height: 1.45em;
  background: #181818 !important;
  color: #F8F8F8 !important;
  span { color: #F8F8F8 !important; }
  span { font-style: normal !important; font-weight: normal !important; }

  .hll { background-color: #ffffcc !important }
  .c { color: #5F5A60 !important; font-style: italic !important } /* Comment */
  .err { border:#B22518; } /* Error */
  .k { color: #CDA869 !important } /* Keyword */
  .cm { color: #5F5A60 !important; font-style: italic !important } /* Comment.Multiline */
  .cp { color: #5F5A60 !important } /* Comment.Preproc */
  .c1 { color: #5F5A60 !important; font-style: italic !important } /* Comment.Single */
  .cs { color: #5F5A60 !important; font-style: italic !important } /* Comment.Special */
  .gd { background: #420E09 } /* Generic.Deleted */
  .ge { font-style: italic !important } /* Generic.Emph */
  .gr { background: #B22518 } /* Generic.Error */
  .gh { color: #000080 !important; font-weight: bold !important } /* Generic.Heading */
  .gi { background: #253B22 } /* Generic.Inserted */
  .go {  } /* Generic.Output */
  .gp { font-weight: bold !important } /* Generic.Prompt */
  .gs { font-weight: bold !important } /* Generic.Strong */
  .gu { color: #800080 !important; font-weight: bold !important } /* Generic.Subheading */
  .gt {  } /* Generic.Traceback */
  .kc {  } /* Keyword.Constant */
  .kd { color: #e9df8f !important;  } /* Keyword.Declaration */
  .kn {  } /* Keyword.Namespace */
  .kp { color: #CF6A4C !important } /* Keyword.Pseudo */
  .kr {  } /* Keyword.Reserved */
  .kt {  } /* Keyword.Type */
  .m { } /* Literal.Number */
  .s { color: #8F9D6A !important } /* Literal.String */
  .na { color: #F9EE98 !important } /* Name.Attribute */
  .nb { color: #CDA869 !important } /* Name.Builtin */
  .nc { color: #9B703F !important; font-weight: bold !important } /* Name.Class */
  .no { color: #7587A6 !important } /* Name.Constant */
  .nd { color: #7587A6 !important } /* Name.Decorator */
  .ni { color: #CF6A4C !important; font-weight: bold !important } /* Name.Entity */
  .nf { color: #9B703F !important; font-weight: bold !important } /* Name.Function */
  .nn { color: #9B703F !important; font-weight: bold !important } /* Name.Namespace */
  .nt { color: #CDA869 !important; font-weight: bold !important } /* Name.Tag */
  .nv { color: #7587A6 !important } /* Name.Variable */
  .o { color: #CDA869 !important } /* Operator */
  .ow { color: #AA22FF !important; font-weight: bold !important } /* Operator.Word */
  .w { color: #141414 !important } /* Text.Whitespace */
  .mf { color: #CF6A4C !important } /* Literal.Number.Float */
  .mh { color: #CF6A4C !important } /* Literal.Number.Hex */
  .mi { color: #CF6A4C !important } /* Literal.Number.Integer */
  .mo { color: #CF6A4C !important } /* Literal.Number.Oct */
  .sb { color: #8F9D6A !important } /* Literal.String.Backtick */
  .sc { color: #8F9D6A !important } /* Literal.String.Char */
  .sd { color: #8F9D6A !important; font-style: italic !important; } /* Literal.String.Doc */
  .s2 { color: #8F9D6A !important } /* Literal.String.Double */
  .se { color: #F9EE98 !important; font-weight: bold !important; } /* Literal.String.Escape */
  .sh { color: #8F9D6A !important } /* Literal.String.Heredoc */
  .si { color: #DAEFA3 !important; font-weight: bold !important; } /* Literal.String.Interpol */
  .sx { color: #8F9D6A !important } /* Literal.String.Other */
  .sr { color: #E9C062 !important } /* Literal.String.Regex */
  .s1 { color: #8F9D6A !important } /* Literal.String.Single */
  .ss { color: #CF6A4C !important } /* Literal.String.Symbol */
  .bp { color: #00aaaa !important } /* Name.Builtin.Pseudo */
  .vc { color: #7587A6 !important } /* Name.Variable.Class */
  .vg { color: #7587A6 !important } /* Name.Variable.Global */
  .vi { color: #7587A6 !important } /* Name.Variable.Instance */
  .il { color: #009999 !important } /* Literal.Number.Integer.Long */
}
```

I had to remove the whole `.pre-code` block inside `sass/partials/_syntax.scss` because some styles kept bleeding through the css cascade and I didn't want to waste more time chasing after them. You can see 
[the complete commit in GitHub](https://github.com/cabeca/cabeca.github.io/commit/7fc9690ace63e9373bce8010f8a68016fce549dc).

The syntax highlighting is not exactly the same as Sublime's but is pretty close. And there you have it, beautiful code in Octopress.

