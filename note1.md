# Custom Django User Models: AbstractUser vs AbstractBaseUser Explained
## 1.Introduction
- Example: "Django’s built-in `User` model works well for many projects, but sometimes you need more control. Maybe you want to use an email instead of a username to log in, or you want to store additional profile data. That’s where Django’s `AbstractUser` and `AbstractBaseUser` come in."

## 2. What is `AbstractBaseUser`?
- "Does not come with fields like `username`, `email`, `first_name`, etc."
- "Gives you password handling, `last_login`, and auth methods like `set_password()` and `check_password()`"
- "You have to define everything else yourself (fields, managers, etc.)"
### Code Example
```
from django.contrib.auth.models import AbstractBaseUser, BaseUserManager
from django.db import models

class CustomUserManager(BaseUserManager):
    def create_user(self, email, password=None):
        if not email:
            raise ValueError("Users must have an email address")
        user = self.model(email=email)
        user.set_password(password)
        user.save()
        return user

class CustomUser(AbstractBaseUser):
    email = models.EmailField(unique=True)
    full_name = models.CharField(max_length=255)

    USERNAME_FIELD = 'email'

    objects = CustomUserManager()
```

## 3. What is `AbstractUser`?
- "Includes all default fields (`username`, `email`, `first_name`, `last_name`, etc.)"
- "Still allows you to add extra fields"
- "Easier to use when you just need to extend the default user model a bit"

### Code Example
```
from django.contrib.auth.models import AbstractUser
from django.db import models

class CustomUser(AbstractUser):
    bio = models.TextField(blank=True)
```

## 4. When to Use Which One?

| Feature | AbstractBaseUser                        | AbstractUser        |
|--------|---------------------------------|--------------------|
| Built-in fields	    | ❌ No                   | 	✅ Yes    |
| Customization flexibility	   | ✅ Full control            | ⚠️ Moderate (adds to base) |
| Ease of use	    | ⚠️ More complex | ✅ Easier to use   |
| Recommended for	  | Fully custom user systems	   | Minor modifications|

## 5. Conclusion
- "Use `AbstractUser` if you're just adding a few fields."
- "Use `AbstractBaseUser` if you want to build a user model from scratch.