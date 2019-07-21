
Hi guys,

This is my second post in the series "Clean Code". In my [first post](https://dev.to/danialmalik/a-beginner-s-guide-to-clean-code-part1-naming-conventions-139l) I talked about how to name variables in a "clean" way. Now I am going to talk about "Clean Functions" and just like last time, these points are derived from the book "Clean Code" by Robert C. Martin.

Below are some key points that should be kept in mind when writing functions. So let's start.

## Functions should be small !!!
This is the first rule for writing functions. The second rule for writing functions is that they should be even _smaller_. Small functions are easy to understand and their intention is clear.

Below is an sample scrapy code written in python.
```python
def get_product(html_response):
   title = html_response.css('.title').html()
   price = html_response.css('.price').html()
   try:
        price = int(price)
   except ValueError:
        price = None

   sizes_html = list(html_response.css('.product .size').html())
   sizes = [parse_size(html_size) for html_size in sizes_html]
   # ...and so on
```
we could divide same login into smaller function like this:
```python

def get_product(html_response):
   title = get_title(html_response)
   price = get_price(html_response)
   sizes = get_product_sizes(html_response)
   # ...and so on

def get_title(html_response):
   return html_response.css('.title').html()

def get_price(html_response):
   price = html_response.css('.price').html()
   try:
        price = int(price)
   except ValueError:
        price = None
   return price

def get_product_sizes(html_response):
   sizes_html = list(html_response.css('.product .size').html())
   sizes = [parse_size(html_size) for html_size in sizes_html]
   return sizes
   ```
In this way it is easier to debug and refactor the code when their is some change in target website.

### Block and indenting
By keeping the functions small, it also implies that we keep the code block inside `if`, `else` and `while` statements minimal, preferably up to one and that one line should be a function call. Similarly, we should avoid nesting such structures inside functions by moving the nested login into another function.

## Do one thing! (Single Responsibility Principle)
> FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY.

One function must do only one thing. If a function is doing more than one things, the function should be split up into more functions.
This can be illustrated with the help of an example below:
```python
def generate_weather_report(date):
   data = weather_client.get_date(date)
   if not data:
       logger.error('Error!')
   else:
       report_data = {}
       # logic to get required data into report_data dict.
       store_into_db(report_data)
```
This function is clearly doing more than one tasks.
- Get data from web.
- validate data
- generate data
- store data into db

This could be refactored as following code:

```python
data = get_weather_data(date)
is_valid = validate_data(data)
if is_valid:
   report_data = generate_weather_report(data)
   store_into_db(data)
else:
   logger.error('Error!')

def get_weather_data(date):
   return  weather_client.get_date(date)

def generate_weather_report(date):
       report_data = {}
       # logic to get required data into report_data dict.
       return report_data
```
In this way our function `generate_weather_report` is doing only one thing.

### Sections
Similarly different logical sections in a function such as initializations, declarations, validations, and actual login should be divided into functions as well.

## Switch statements
*Please note that there are no switch statements in python. The example below is for only explanation purposes.*

It is very hard to make small `switch` statements as the purpose of `switch` statements is to do _`N`_ things.
In some cases `switch` statements can be avoided.
For example:
```python
def month_index_to_string(index):
   switch index:
       case 1:
           return "January"
       case 2:
           return "February"
       default:
           return "INVALID"
       ...
```
can be written as:
```python
MONTHS = {
   1: "January",
   2: "February",
   ...
}

def month_index_to_string(index):
   try:
       return MONTHS[index]
   except KeyError:
       return "INVALID"
```

## Use descriptive names
The intention of the function should be clear from its name and its implementation must not deviate from its name. Do not afraid to use long function names. Ideally function names should start with a verb e.g. `get_employees_data`, `delete_employee` etc.
You can find more details on function names in my [first post](https://dev.to/danialmalik/a-beginner-s-guide-to-clean-code-part1-naming-conventions-139l) post.


## Function Arguments
Ideally, a function should have zero arguments (niladic functions). Next comes one (monadic), then comes two (dyadic) and finally three (triadic) argument functions which should be avoided whenever possible. More than three-argument (polyadic) functions must not be used if possible.
Several arguments could mean that function is doing more than one thing. It is also hard from a testing point of view to test functions with many arguments as it would be hard (or nearly impossible) to test all combinations of arguments.
If you have to pass a lot of data to the function, try sending that data through an object.
```python
def evaluate_person_salary(person_id, hours_worked, hourly_rate, overtime_rate)
   #logic
```
This is absolutely evil. It should be written as:
```python
# person_id, hours_worked, hourly_rate, overtime_rate are data members of the person_data object.
def evaluate_person_salary(person_data, hours_worked):
   #logic
```
If in some scenarios you cannot group the arguments into a single object, you are doing something wrong and that function should be split up.

### Flag Argument
A flag argument is horrible and should never be used. Passing a `boolean` into a function means that function is doing more than one tasks which is a clear violation of the _SRP_ principle.

### Arguments Lists
Sometimes we want to pass a variable number of arguments to a function e.g. `"{}:{}-{}".format(date, person, message)`.
If these arguments are treated identically then these are considered to be a single argument (`List` in this case).

## Have No Side Effect
One function must do only one thing with no other side effects. Sometimes we think that our function is doing only one thing but actually it is doing more than one thing and those other things are its side effects. consider this example below:
```python
def check_password(username, password):
   if authenticate_user(username, password):
       return True
   else:
       return False
...
def authenticate_user(username, password):
   if User.objects.get(username=username).check_password(password):
       login(username)
       return True
   return False
```
Here it seems that `check_password` is doing only one thing, checking the password, but when we see at the definition of `authenticate_user` we come to know that it's logged the user in also.

## The Stepdown Rule
The author's stepdown rule is very helpful for writing easily readable code. According to this rule, you should write a function in such a way that function calls should be followed by their definitions in the code below.

In this way, while reading a code file, when you see a function call you don't have to scroll up to find its definition because you will find its definition down below while reading through the code at a constant pace.
This approach is illustrated below in the next section's example.


## DON'T REPEAT YOURSELF
This is one of the most important principles of programming. You should never repeat yourself. If you have written a class function that is needed elsewhere too, move it out of class and make it a utility/helper function. If the same function is needed in two or more classes, make a parent class with that function and inherit other classes from it.

I had to use this approach quite recently when I was writing some test cases for the project. The setup process for the test cases was mostly the same so I decided to make a base class with the `setUp` function and then inherited other test cases from it. In this way, I didn't have to repeat myself for all the test cases.
An abstract code of the `setUp` is described below. You will notice the _stepdown_ approach too. You don't have to move up and down again and again to understand the code.

```python
class SampleTestsBaseClass(TestCase):
   """Base class for all test cases of "Sample" module."""

   def setUp(self):
       """Setup all test data required for testing any of the test cases. """
       super(SampleTestsBaseClass, self).setUp()
       self.topic = self._create_topic()
       self.course = self._create_course()
       self.team = self._create_team(self.course.id, topic_id=self.course.teams_topic['id']))
       self.user = self._create_user()
       self.team_membership = self._create_team_membership(self.team, self.user)

   def _create_topic(self):
       """ Return a topic dict. """
       topic = {u'name': u'Topic', u'description': u'The best topic!', u'id': u'0', 'url': 'example.com/topic/0'}
       return topic

   def _create_course(self, **kwargs):
       """ Create and return a course with test data """
       course = CourseFactory.create(kwargs)
       return course

   def _create_team(self, course_id, topic_id):
       """ Create a CourseTeam for provided course_id and topic_id
       """
       team = CourseTeamFactory.create(
           course_id=course_id,
           topic_id=topic_id,
           name='Test Team',
           description='Testing Testing Testing...'
       )
       return team

   def _create_user(self, username, password, **kwargs):
       user = UserFactory.create(username=username, password=password, **kwargs)
       return user

   def _create_team_membership(self, team, user):
       """Create a team membership object that is responsible for joining of a user in a team.
       """
       membership = CourseTeamMembershipFactory.create(team=team, user=user)
       return membership

# Now inherit the test cases from the above base class.
class Module1TestCase(SampleTestsBaseClass):
   ...

class Module2TestCase(SampleTestsBaseClass):
   ...
```
Now I can inherit all other related test cases with this _BaseClass_ and won't have to repeat the _`setUp`_ code.


## Conclusion
Functions are the basic building blocks of any system. These are verbs of the system while classes are the nouns. This post was about writing the functions in such a way that these are easy to read, to understand, to maintain and to refactor. Functions should be implemented while keeping the _DRY_ (Don't Repeat Yourself) principle. Hence by following the above principles and practices your functions will be descriptive, clean and beautifully organized.
