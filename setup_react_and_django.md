## Setup React and Django On The Same Server

When developing in React and Django, a development server will be running for each respectively. However, when deploying, it may be required to have them on the same server. The following details how to set that up.

I am using Create-React-App for this.

1. Create the Virtual Environment, start the Django Project
2. Within the same folder you created your Django Project (manage.py should also be in this folder), start your React App using create-react-app
    ```bash
    npx create-react-app frontend .
    ```

    Folder structure should look something like this:
    ```
    parent_folder
    |_
      |-django_project_folder
      |-frontend
      |   |-...reactFiles
      |-manage.py

    ```
3. In the settings.py file add the following at the bottom of the file
   ```python3
   STATICFILES_DIRS = (
    os.path.join(os.path.join(BASE_DIR, 'frontend'), 'build', 'static'),
    )
   ```
   This points the static files to the build folder create when running `yarn build` for the frontend

4. In the Django project folder (the one containing wsgi.py) create a views.py file and add the follow:
   ```python3
    from django.views.generic import View
    from django.http import HttpResponse
    from django.conf import settings
    import os


    class ReactAppView(View):

        def get(self, request):
            print(os.path.join('frontend', 'build', 'index.html'))
            try:
                with open(os.path.join('frontend', 'build', 'index.html')) as file:
                    return HttpResponse(file.read())

            except:
                return HttpResponse(
                    """
                    index.html not found ! build your React app !!
                    """,
                    status=501,
                )
   ```
   This looks at the files created by `yarn build` and serves the index.html file (which should be the base file for the React app)

5. In the project urls.py file, add the follow:
   ```python 3
    from .views import ReactAppView

    path("", ReactAppView.as_view()),
   ```