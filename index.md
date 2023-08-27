---
layout: page
title: The Missing Semester of Your CS Education
nositetitle: true
---

<div class="note">
This document has been modified from the original site developed by MIT to be
more relevant to UoB's CS department.  Boxes like this will indicate notes or
departures from the original content.
<br /><br />
In this process, we've removed some MIT specific stuff but only to minimize confusion among UoB students.  The full history including all our modifications can be viewed <a href="https://github.com/afnom/missing-semester">here</a>.
</div>

Classes teach you all about advanced topics within CS, from operating systems
to machine learning, but there’s one critical subject that’s rarely covered,
and is instead left to students to figure out on their own: proficiency with
their tools. We’ll teach you how to master the command-line, use a powerful
text editor, use fancy features of version control systems, and much more!

Students spend hundreds of hours using these tools over the course of their
education (and thousands over their career), so it makes sense to make the
experience as fluid and frictionless as possible. Mastering these tools not
only enables you to spend less time on figuring out how to bend your tools to
your will, but it also lets you solve problems that would previously seem
impossibly complex.

Read about the [motivation behind this class](/about/).

# Practicalities

* **Staff**: This class is co-taught by Jesse, Freddie, Oliver, TBD
* **Lecture**: ROOM TBD, TIME TBD
* **Questions / Discussions**: Please join the [Missing Semester Discord](http://www.google.com)!

# Schedule

<ul>
{% assign lectures = site['2023'] | sort: 'date' %}
{% for lecture in lectures %}
    {% if lecture.phony != true %}
        <li>
        <strong>{{ lecture.date | date: '%-m/%d/%y' }}</strong>:
        {% if lecture.ready %}
            <a href="{{ lecture.url | relative_url }}">{{ lecture.title }}</a>
        {% else %}
            {{ lecture.title }} {% if lecture.noclass %}[no class]{% endif %}
        {% endif %}
        </li>
    {% endif %}
{% endfor %}
</ul>

Video recordings (of the original MIT lectures) are available [on YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J).

# Beyond MIT

<div class="note">
Please note -- these links pertain only to the original MIT content which may have been altered.
</div>


We've also shared this class beyond MIT in the hopes that others may
benefit from these resources. You can find posts and discussion on

 - [Hacker News](https://news.ycombinator.com/item?id=22226380)
 - [Lobsters](https://lobste.rs/s/ti1k98/missing_semester_your_cs_education_mit)
 - [/r/learnprogramming](https://www.reddit.com/r/learnprogramming/comments/eyagda/the_missing_semester_of_your_cs_education_mit/)
 - [/r/programming](https://www.reddit.com/r/programming/comments/eyagcd/the_missing_semester_of_your_cs_education_mit/)
 - [Twitter](https://twitter.com/jonhoo/status/1224383452591509507)
 - [YouTube](https://www.youtube.com/playlist?list=PLyzOVJj3bHQuloKGG59rS43e29ro7I57J)

# Translations

- [Chinese (Simplified)](https://missing-semester-cn.github.io/)
- [Chinese (Traditional)](https://missing-semester-zh-hant.github.io/)
- [Japanese](https://missing-semester-jp.github.io/)
- [Korean](https://missing-semester-kr.github.io/)
- [Portuguese](https://missing-semester-pt.github.io/)
- [Russian](https://missing-semester-rus.github.io/)
- [Serbian](https://netboxify.com/missing-semester/)
- [Spanish](https://missing-semester-esp.github.io/)
- [Turkish](https://missing-semester-tr.github.io/)
- [Vietnamese](https://missing-semester-vn.github.io/)
- [Arabic](https://missing-semester-ar.github.io/)
- [Italian](https://missing-semester-it.github.io/)
- [Persian](https://missing-semester-fa.github.io/)

Note: these are external links to community translations. We have not vetted them.

Have you created a translation of the course notes from this class? Submit a
[pull request](https://github.com/missing-semester/missing-semester/pulls) so
we can add it to the list!

## MIT Acknowledgements

We thank Elaine Mello, Jim Cain, and [MIT Open Learning](https://openlearning.mit.edu/) for making it possible for us to record lecture videos; Anthony Zolnik and [MIT AeroAstro](https://aeroastro.mit.edu/) for A/V equipment; and Brandi Adams and [MIT EECS](https://www.eecs.mit.edu/) for supporting this class.

---

<div class="small center">
<p><a href="https://github.com/afnom/missing-semester">Source code</a>.</p>
<p>Licensed under CC BY-NC-SA.</p>
<p>See <a href="{{'/license/' | relative_url}}">here</a> for contribution &amp; translation guidelines.</p>
</div>
