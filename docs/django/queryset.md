# Custom QuerySet

## Overview

A QuerySet is Django's way of describing which records you want from the database.

```python
Book.objects.filter(status="published")
```

A Custom QuerySet is when you give commonly used queries a meaningful name.

```python
# models.py
class BookQuerySet(models.QuerySet):
    def published(self):
        return self.filter(status="published")

# views.py
publihsed_books = Book.objects.published()
```

- **QuerySet**: a Django object used to build a query for a set of database records.
- **Custom QuerySet**: a QuerySet with your own named methods for reusable application queries.

> Custom QuerySets aren't a different kind of database query. They're a way to give reusable query logic a name.

---

## QuerySet and FilterSet

>QuerySet = define reusable ways the application wants to retrieve data.
>QuerySet/Manager = application-level data access and reusable database logic.

>FilterSet = let external/user input choose how to narrow that data.
>FilterSet = translating user-provided filtering criteria into QuerySet operations.

>Put reusable, domain-specific query behavior on custom QuerySets. Use FilterSets when external input needs to select/filter that data.

| Situation                                        | Custom QuerySet? | Plain `.filter()`? | FilterSet? | Example                                        |
| ------------------------------------------------ | ---------------: | -----------------: | ---------: | ---------------------------------------------- |
| One-off query used once                          |                ❌ |                  ✅ |          ❌ | `Book.objects.filter(author=author)`           |
| Simple query in a view                           |                ❌ |                  ✅ |          ❌ | `Book.objects.filter(status="published")`      |
| Same query logic used in several places          |                ✅ |                  ❌ |          ❌ | `.published()`                                 |
| Query represents a business/domain concept       |                ✅ |                  ❌ |          ❌ | `.available()`, `.published()`                 |
| Query needs parameters                           |                ✅ |                  ❌ |          ❌ | `.by_author(author)`                           |
| Combining reusable query rules                   |                ✅ |                  ❌ |          ❌ | `.published().available()`                     |
| User chooses filters through URL/API params      |                ❌ |                  ❌ |          ✅ | `?author=3&year_min=2020`                      |
| Many optional user-controlled filters            |                ❌ |                  ❌ |          ✅ | search + author + genre + year                 |
| Need `select_related()` repeatedly               |               ✅* |                  ❌ |          ❌ | `.with_author()`                               |
| Need `prefetch_related()` repeatedly             |               ✅* |                  ❌ |          ❌ | `.with_genres()`                               |
| Complex database query reused by the application |                ✅ |                  ❌ |          ❌ | `.currently_available()`                       |
| Need to optimize a slow query                    |        Sometimes |          Sometimes |  Sometimes | `select_related`, indexes, query restructuring |


## Example

- Model:

```python
# models.py 
from django.db import models 

class Author(models.Model): 
    name = models.CharField(max_length=100) 
    
    def __str__(self): return self.name 
    
class BookQuerySet(models.QuerySet): 
        
    def published(self): 
        return self.filter(is_published=True) 
    
    def by_author(self, author): 
        return self.filter(author=author) 
    
    def with_author(self): 
        return self.select_related("author") 
        
class Book(models.Model): 
    title = models.CharField(max_length=200) 
    author = models.ForeignKey( Author, on_delete=models.CASCADE, related_name="books", )published_year = models.PositiveIntegerField() 
    is_published = models.BooleanField(default=True) objects = BookQuerySet.as_manager() 
    
    def __str__(self): 
            return self.title
```

- FilterSet:

```python
# filters.py 
import django_filters 
from .models import Book 

class BookFilter(django_filters.FilterSet): 
    title = django_filters.CharFilter( field_name="title", lookup_expr="icontains", ) 
    author = django_filters.ModelChoiceFilter( queryset=Author.objects.all(), ) 
    published_year = django_filters.NumberFilter() 
    
    class Meta: 
        model = Book 
        fields = [ "title", "author", "published_year", ]
```

- Views:

```python
# views.py 

from django.views.generic import ListView 
from .filters import BookFilter 
from .models import Book 

class BookListView(ListView): 
    model = Book template_name = "books/book_list.html" 
    context_object_name = "books" 
    
    def get_queryset(self): 

        queryset = ( Book.objects.published().with_author() ) 
        
        self.filterset = BookFilter(self.request.GET, queryset=queryset,) 
        
        return self.filterset.qs
```

---

- QuerySet:

>The application says:

> `Book.objects.published().with_author()`

>"When displaying books, I want published books, and I want their authors loaded efficiently."

- FilterSet:

>The user says:

>```python
>title=django
>author=3
>```

>"Of those books, I want the ones matching my criteria."

```text
┌──────────────────────────────────────────────┐
│ Custom QuerySet                             │
│                                              │
│ "What does the APPLICATION want?"            │
│                                              │
│ .published()                                 │
│ .with_author()                               │
│ .by_author()                                 │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
                 Base QuerySet
                       │
                       ▼
┌──────────────────────────────────────────────┐
│ FilterSet                                    │
│                                              │
│ "What does the USER want to narrow down?"    │
│                                              │
│ ?title=django                                │
│ ?author=3                                    │
│ ?published_year=2025                         │
└──────────────────────┬───────────────────────┘
                       │
                       ▼
                Filtered QuerySet
```

QuerySet = reusable application query vocabulary.

FilterSet = flexible external/user filtering.

Neither replaces the other; they compose together.