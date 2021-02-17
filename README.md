## Deploy Django Project to Heroku
1. Create Heroku Account : https://www.heroku.com/
2. Download and Install Git : https://git-scm.com/downloads
3. Download and Install Heroku CLI : https://devcenter.heroku.com/articles/heroku-cli#download-and-install
4. Open Terminal
5. Login into Heroku CLI. Run below command it will open Browser then Click on Login 
    ```javascript
      heroku login 
    ```
6. Create Repo for Project
    ```javascript
      git init
    ```
7. Add All Files to Repo
    ```javascript
      git add . 
    ```
8. Commit All Changes
    ```javascript
      git commit -m "any comment"
    ```
9. Create an App using Dashboard or Shell (I am creating using Shell)
    ```javascript
      heroku create heroku_app_name
    ```
10. Set Repo
     ```javascript
      heroku git:remote -a heroku_app_name
      ```
11. Install gunicorn or waitress - This will be our production server as we can not use development server which we were using by runing python manage.py runserver. Waitress is meant to be a production-quality pure-Python WSGI server with very acceptable performance. For More: https://docs.pylonsproject.org/projects/waitress/en/latest/ 
     ```javascript
      pip install waitress
     ```
12. Run wsgi.py file using waitress to test everything works fine on Local Machine (Before Pushing to Heroku)
     ```javascript
      waitress-serve --port=8000 inner_project_folder_name.wsgi:application
     ```
13. You will get a link in terminal just open it. If everything works then you will be able to see your project running on Web Browser
14. If you get an error: Disallowed Host Invalid HTTP_HOST header then do below change in Django's Settings.py file and re-run wsgi.py file as Step 12
     ```python
      ALLOWED_HOSTS = ['*']
     ```
15. Create a file named **Procfile** then write below code in the file
    ```javascript
      web: waitress-serve --port=8000 inner_project_folder_name.wsgi:application
    ```
16. Run below command - This will use Procfile to run the project. You will see an URL open it if everything file you will see project in browser 
    ```javascript
      heroku local
    ```
17. Now go to your Django project's settings and do below change
    ```python
      DEBUG = False
      ALLOWED_HOSTS = ['heroku_app_name.herokuapp.com', 'localhost']
    ```
    > As you have created an Heroku App so you have your app url e.g. https://heroku_app_name.herokuapp.com/ You can find it follwoing Heroku's Dashboard -> Setting
18. We will also create Config Var for Django Project's Secret Key by following
    1. Copy SECRET_KEY from Django's settings.py File
    2. Go to Heroku App Setting then click Reveal Config vars then write
        ```python
	        SECRET_KEY r6t3d0udsdsdew5656+u9d+%o#^uo0su-i3x3_5zs5-5r7r9a1_mhwfi!2b+^
        ```
	  3. Click Add
	  4. Go to Django Project Settings.py and do below changes
          ```python
              import os
	          SECRET_KEY = os.environ['SECRET_KEY']
          ```
        
19. If you have static files must include STATIC_ROOT in Django's settings.py file
    ```python
      STATIC_ROOT = BASE_DIR / "static"
    ```
20. Install whitenoise - WhiteNoise allows your web app to serve its own static files, making it a self-contained unit that can be deployed anywhere without relying on nginx, Amazon S3 or any other external service. (Especially useful on Heroku, OpenShift and other PaaS providers.) For More: http://whitenoise.evans.io/en/stable/
    ```javascript
      pip install whitenoise
    ```
21. Open Django's settings.py file and Add Whitenoise Middleware
    ```python
      MIDDLEWARE = [
        # 'django.middleware.security.SecurityMiddleware',
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # ...
      ]
    ```
22. Bundle all requirements
    ```javascript
      pip freeze > requirements.txt
    ```
23. Make sure you have changed **web: waitress-serve --port=8000 inner_project_folder_name.wsgi:application** to **web: waitress-serve --port=$PORT inner_project_folder_name.wsgi:application** in **Procfile** before pushing to heroku
24. Run below command
    ```javascript
      git add .
      git commit -m "any comment"
      git push heroku master
    ```
  25. Done
  
  
## Common Error while Deploying
Error 1: django-assets rejected <br>
Cause: You haven't specified STATIC_ROOT <br>
Solution: Either Provide STATIC_ROOT or Disable it bu running heroku config:set DISABLE_COLLECTSTATIC=1 <br>


Error 2: No web processes running <br>
Cause: Unable to find wsgi or havent configured Procfile <br>
Solution: Config Properly Procfile <br>
