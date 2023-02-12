# Live Project

## Introduction
For the last two weeks of my time at the Tech Academy, I worked among other students in a team where, using the django framework, we developed 
a project that acts as an interactive website for managing one's collections of things related to various topics. Each member was asked to choose 
a topic that a person might want to keep track of and create a web app to manage data entered by the user as well as create a page where content is
displayed to  the user from an outside source by web scraping from an API. This project taught me a lot about the software development industry
as an individual programer and as part of a developement team as it was managed using the Agile/Scrum methodology during its process. The Project
consisted of a two week sprint, during this time, each student was required to attend: a sprint planning session at the start of the sprint, daily stand-up
meetings at the beggginning of each day, and a sprint review at the end of the sprint. The project was also broken down into ten stories, each being an individual 
feature of the main task being asked for. Over the two week sprint I learned a lot about undividual strengths and weaknesses, I also had the opportunity to work under a project team environment where one's changes could affect the overall project.

Below are descriptions the main tasks some of the stories consisted on, along with code snippets, pictures and navigation links.

*[CRUD Functionality](#crud-functionality)
*[API](#API)
*[Front End Development](#front-end-development)

### CRUD Functionality
This task consisted on figuring out various elements of the object being tracked and create a model, a model form, and develop CRUD functionality to manage 
the data being entered by the user. I decided to create a Web apllication where the user could add locations in Costa Rica that they consider worth visiting. I created a model with different categories for the user to fill out when a location is addded.

       # Creates model of Personal Locations
       class Location(models.Model):
          Location_Name = models.CharField(max_length=100, default="", blank=False, null=False)
          Province = models.CharField(max_length=11, default="", blank=False, null=False, choices=PROVINCE_CHOICES)
          City = models.CharField(max_length=100, default="", blank=False, null=False)
          Cost_of_Entry = models.CharField(max_length=5, default="", blank=False, null=False, choices=COST_CHOICES)
          Cost_Amount = models.DecimalField(default=0.00, max_digits=100000, decimal_places=2)
          Description = models.CharField(max_length=500, default="", blank=False, null=False)

          # Creates model manager
          Locations = models.Manager()

          # Displays the object output values in string format
          def __str__(self):
              # Returns the input value of LocationName in the browser instead of default titles
              return self.Location_Name


I then created a base html page and, by using template inheridtance along with template tags, I created a home page among other pages that allows the user to add, edit, and delete locations within the app. Then, using URL routing, each of the pages urls were mapped to a view based function to display the desired content when the user requests a page within the app. Below is an example the page to add a location followed by the function used that renders the page when requested by the user:
  
  
        {% extends "costarica_base.html" %}


        {% block title %}
            Ticolandia|Add
        {% endblock %}


        {% block header %}
            <span class="head-main">
                Add your own location
            </span>
            <h1>
                Costa Rica is full of beautiful scenery and fun attractions!<br>
                <br>
                With this form you can add a spot you would like to share with others<br>
                There can never be too many!
            </h1>
        {% endblock %}


        {% block content %}
            <div class="text-center">
                <h1 class="link-headers">Location Form</h1>
                <div class="form-container">
                    <form method="POST">
                        {% csrf_token %}
                        {{form.as_p}}
                        <div class="btn-container">
                            <button class="btn" type="submit">Add Location</button>
                            <button class="btn" type="submit"><a href="{% url 'costarica_home' %}">Cancel</a></button>
                        </div>
                    </form>
                </div>
            </div>
        {% endblock %}
        
        
        # This function will render the addLocation page when requested
        def add_location(request):
            form = LocationForm(data=request.POST or None)
            if request.method == 'POST':
                if form.is_valid():
                    form.save()
                    return redirect('../show')
            content = {'form': form}
            return render(request, 'CostaRicaApp/addLocation.html', content)

### API
Another one of the tasks was to create a page within the app where information from an API was presented to the user using JSON responses and url/http 
queries that relates to the chosen topic in some way. As I added a "Cost" field within the form to add locations, I decided to use an API that realtes to currency rate exchanges to allow users to select from a list menu of a currency and view the exchange rate the chosen currency to Colones (the currency used in Costa Rica. I created a page template and a fuction to render the API page when requested. Withing the template, I created a select menu that lists the top ten currencies used around the world as a way to get user input information. Then, by parsing through the JSON file returned by the API, I querried specific information from the API to display the desired exchange rate based on the user's selected currency (user's input). Below I have attached the function used when the API page is requested:

  
        # This function will render the api page when requested
        def api_location(request):
            url = "https://currency-converter-by-api-ninjas.p.rapidapi.com/v1/convertcurrency"

            if request.GET.get('code'):  # Get user input
                have = request.GET['code']  # set the have parameter on query equal to user input
            else:
                have = 'USD'  # Set 'USD' as default 'have' parameter when page is rendered

            querystring = {"have": have, "want": "CRC", "amount": "1"}

            headers = {
                "X-RapidAPI-Key": "e5ad6dc879mshfd9fa5ba4ce258ep164f23jsndf37e1afc231",
                "X-RapidAPI-Host": "currency-converter-by-api-ninjas.p.rapidapi.com"
            }

            response = requests.request("GET", url, headers=headers, params=querystring)

            api_content = json.loads(response.text)

            if request.GET.get('code'):
                have = request.GET['code']

            currency_rate = '{0} {1} currency = {2} Costa Rica Colones'.format(str(api_content["old_amount"]),
                                                                               str(api_content["old_currency"]),
                                                                               str(api_content["new_amount"]))
                                                                               
            rate_info = '1 {0} = {1} CRC'.format(str(api_content["old_currency"]), str(api_content["new_amount"]))
            content = {'currency_rate': currency_rate, 'rate_info': rate_info}

            return render(request, 'CostaRicaApp/costarica_api.html', content)
            
 
### Front End Development
The last task of the project was to work on front end improvements. This required some planning and drawing to figure out how I wanted to make my overall web application to look and behave. Also, since template inheridtance was used throughout the pages, numerours modifications needed to made on each page template to match the page's content (i.e a form, a table, or the content of headings). I wanted my application to have an uniformed look throughout each page; So I decided to use a Picture of a well-known volcano in Costa Rica as the back-ground, then provided the user a title and a brief description of each page content using heading and paragraph tags that changed depending on the page requested while keeping have that uniformed look. Then, using CSS I added final touches such ass fonts and colors for each of the Ids, classes and elements within the pages, as well as Navbar, buttons, and overall page animations. Below I have attached a GIF to display the final product:

       ![img](ezgif.com-video-to-gif.gif)

