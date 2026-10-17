# Index

- [Intro](#intro)
  - [MVT (Model-View-Template)](#mvt-model-view-template)
  - [Signals](#signals)
  - [Example](#example)


# Intro

Python framework with build in ORM, templating engine, admin panel


## MVT (Model-View-Template)

- Model- Data and database logic. Define the structure of your database tables. Interacts with the database via Django’s ORM.
- View- Handles business logic. Fetches data from Models. Sends data to Templates or returns JSON responses (for APIs).
- Template- Handles presentation — the HTML, CSS, or frontend logic. Receives data from views and displays it to the user.


## Signals

- Signals allow certain senders to notify a set of receivers when specific actions occur.
- They are used to trigger custom behavior in response to events such as creating, updating, or deleting a database entry.
- Similar to triggers in db


## Example

```python
# ==========================================================
# models.py
# ==========================================================
from django.db import models
from django.contrib.auth.models import User

# Simple Post model with author relation
class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title


# ==========================================================
# serializers.py
# ==========================================================
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = '__all__'
        read_only_fields = ['author', 'created_at']


# ==========================================================
# permissions.py
# ==========================================================
from rest_framework import permissions

# Custom permission: only author can edit or delete
class IsAuthorOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        # SAFE_METHODS = GET, HEAD, OPTIONS (read-only)
        if request.method in permissions.SAFE_METHODS:
            return True
        return obj.author == request.user


# ==========================================================
# views.py
# ==========================================================
from rest_framework import viewsets, permissions
from .models import Post
from .serializers import PostSerializer
from .permissions import IsAuthorOrReadOnly

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all().order_by('-created_at')
    serializer_class = PostSerializer
    permission_classes = [permissions.IsAuthenticatedOrReadOnly, IsAuthorOrReadOnly]

    # Automatically set author as the logged-in user
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)


# ==========================================================
# urls.py (app-level)
# ==========================================================
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import PostViewSet

router = DefaultRouter()
router.register('posts', PostViewSet, basename='post')

urlpatterns = [
    path('', include(router.urls)),
]


# ==========================================================
# project-level urls.py
# ==========================================================
from django.contrib import admin
from django.urls import path, include
from rest_framework.authtoken import views as drf_auth_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('your_app_name.urls')),
    path('api-token-auth/', drf_auth_views.obtain_auth_token),  # Login to get token
]
```
