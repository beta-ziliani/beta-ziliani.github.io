---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
---

# Research

I got my PhD with honors from the [University of Saarland](https://saarland-informatics-campus.de/) and [Max Plank Institute for Software Systems](https://www.mpi-sws.org), under the supervision of [Dr. Derek Dreyer](https://www.mpi-sws.org/~dreyer). Before that I got my *licenciatura* (~ MSc.) from [FCEyN, UBA (Argentina)](https://exactas.uba.ar/).

Then I moved back to Argentina and got a researcher position at [CONICET](http://www.conicet.gov.ar/) and [FAMAF, UNC](https://famaf.unc.edu.ar), and got promoted to the second stage (two after postdoc). After an impass of 4+ years in [the industry](./development.html), I'm currenlty a researcher at [Universidad ORT Uruguay](https://fi.ort.edu.uy).

| [<i class="fa-solid fa-briefcase"></i> Service](#service) |  [<i class="fa-solid fa-users"></i> Alumni](#alumni) | [<i class="fa-solid fa-book"></i> Publications](#publications) |

<br/>

## <a name="service"></a> <i class="fa-solid fa-briefcase"></i> Service

{% for service in site.data.service %}

- **{{service.title}}:** {% for gig in service.instances %}[{{gig.name}} ({{gig.year}})]({{gig.url}}){% unless forloop.last %}, {% endunless %}{% endfor %}

{% endfor %}

<br/>
## <a name="alumni"></a> <i class="fa-solid fa-users"></i> Alumni

### PhD

{% bibliography --query @phdthesis[tag = student] %}

### Licenciatura (~ MSc.)

{% bibliography --query @masterthesis[tag = student] %}

<br/>
## <a name="publications"></a> <i class="fa-solid fa-book"></i> Publications

### Journals

{% bibliography --query @article %}

### Proceedings

These are all peer-reviewed, long papers.

{% bibliography --query @*[booktitle ^= Proceedings] %}

### Theses

{% bibliography --query @*[tag = thesis] %}
