# PythonProjectApp
 An app that had was a part of a sprint
 
 ## Introduction
 This project was part of a 2 week sprint where I was tasked with creating an application in the Star Wars Universe. I created a webpage which would allow anyone the option to search or add characters in the Star Wars Universe by Name, Film Title, Force Affiliation (light or dark), and Race. Using the swapi API this was achieved greatly.
 
 Note: Because this project was done in close collaboration with my team and fellow students, I may not post the project in its entirety. Instead, I have included my HTML templates, as well as this code summary to document my work and experiences during the two-week live project at the Tech Academy.
 
 
 ## CRUD
 ### Create
 The first thing I had to do was create models for my app. To do this, I used Django's Model class and defined my models' attributes in the models.py file of the app:
 
 ```
 
WHICH_AFFILIATION = (
    ('light', 'light'),
    ('dark', 'dark'),
    ('neutral', 'neutral')
)


class Character(models.Model):
    Name_of_Character = models.CharField(max_length=100)
    First_Appearance = models.CharField(max_length=60, default='', blank=True)
    Race_Type = models.CharField(max_length=60, default='', blank=True)
    Affiliation = models.CharField(max_length=60, default='neutral', choices=WHICH_AFFILIATION)
    Additional_Details = models.TextField(max_length=300, blank=True)

    objects = models.Manager()

    def __str__(self):
        return self.Name_of_Character
        
 ```
 
 I had this all linked to several html pages like this one:
 
 ```
 {% extends "swu_base.html" %}
{% load static %}

{% block title %}Add a Character! {% endblock %}

{% block header %} {% endblock %}

{% block content %}
<h1>Add a Character to the data bank!</h1>
<br><br>
<h3>

      <h1>Enter Character</h1>
    <div class="form-wrap">
        <form method="POST" id="character-form">
            {% csrf_token %}
            {{ form.as_p }}
            <button id="add-button" type= "submit" name="add-char">ADD CHARACTER!</button>
        </form>
        <!--IN FORMS.PY RETURNS INFO FROM MODELS-->
        <br>
    </div>
<br>
</h3>
{% endblock %}
```
And this page to show the full list of characters added to the SQLite database

```
{% extends "swu_base.html" %}
{% load static %}

{% block title %} Characters {% endblock %}

{% block header %} {% endblock %}

{% block content %}
<h1>Characters</h1>
<br><br>
<h3>

    <!-- List of characters need to learn how to add pictures -->
{% for Character in char_list %}
        <ul class="listed">
            <li><a href="{% url 'character_details' Character.id %}">{{ Character.Name_of_Character}} ({{Character.Race_Type}}) ({{Character.Affiliation }})</a></li>
            <a class="function-button" href="{% url 'edit_char_list' Character.id %}">Edit</a> <a  class="function-button" href="{% url 'delete_character' Character.id %}">Delete</a>
        </ul>
{% endfor %}



<br>
</h3>
{% endblock %}
```
I was also tasked with creating a Details page as the character list just showed a few attributes. This woudld allow for someone to click on a name and see further details on a seperate page with edit and delete buttons.

```
{% extends "swu_base.html" %}
{% load static %}
{% block title %} {{ Name_of_Character }} {% endblock %}
{% block content %}

<h2>{{Name}}({{Race}})</h2>
<h2>First Appearance: {{First_Appearance}}</h2>
<h2>Affiliation: {{Affiliation}}</h2>
<h2>Details: {{Details}}</h2>


{% endblock %}
```

## View
To have all the information show on any page from the database. I had to create a views page in views.py 

```
class CharacterViewSet(viewsets.ModelViewSet):
    queryset = Character.objects.all()
    serializer_class = [permissions.IsAuthenticated]
    filter_backends = [filters.SearchFilter]
    search_fields = ['name', 'race']


def characters(request):
    form = CharacterForm(request.POST or None)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect("swu_characters")
        else:
            form = CharacterForm()
    return render(request, 'swu_characters.html',
                  {'form': form}
                  )


def characters_list(request):
    char_list = Character.objects.all()
    return render(request, 'swu_charlist.html', {'char_list': char_list})


def character_details(request, id):
    char_list = get_object_or_404(Character, id=id)
    return render(request, "swu_details.html", {
        'Name': char_list.Name_of_Character,
        'First_Appearance': char_list.First_Appearance,
        'Race': char_list.Race_Type,
        'Affiliation': char_list.Affiliation,
        'Details': char_list.Additional_Details,
    })


def edit_char_list(request, id):
    char_list = get_object_or_404(Character, id=id)
    form = CharacterForm(request.POST or None, instance=char_list)
    if form.is_valid():
        form.save()
        return HttpResponseRedirect('/StarWarsUniverse/' + id)
    return render(request, "swu_edit.html", {"form": form})


def delete_character(request, id):
    char_list = get_object_or_404(Character, id=id)
    form = CharacterForm(request.POST or None, instance=char_list)
    if request.method == "POST":
        char_list.delete()
        return HttpResponseRedirect('/StarWarsUniverse/characters_list')
    return render(request, "swu_delete.html", {"form": form})
```
This shows all the information where it is linked.

## API
This was the SWAPI API function from my views.py
```

def sources(request):
    if request.method == 'POST':
        form = CharacterForm(request.POST)
        pprint(form)
        return HttpResponseRedirect('swu_sources')
    else:
        response = requests.get(f'https://swapi.dev/api/people')
        form = response.json()
        search_term = CharacterForm(request.POST)['Name_of_Character'].value()
        print(search_term)
        pprint(form['count'])
    pprint(request)
    return render(request, 'swu_sources.html',
                  {'form': form['results'][0]['name']}
                  )
                  
```

## Used in App
The app is created in the Django Framework version 2.2 and was written with Python, HTML, CSS, & DTL (Django-Template Language). Using Azure for Version Control.

## Skills Aquired
This was one of my favorite projects I worked on. With this project I gained the confidence to create a fully functional Django application. I faced many difficulties during the sprint but I never faultered. Working closely with my team assured me I wasn't alone and that many challenges I faced, others faced as well. Version control was a bit tricky. I feel like I grew exponentially as a developer and cannot thank Prosper IT Consulting enough for giving me this opportunity.
