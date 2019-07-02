# The problem

"Naming" is a very important step of any process. We put a lot of thought into naming our children because we understand its importance. But when we do the same for our code, we don't have enough time for it. We either think that we already know the best naming practices or we just don't think that it's worth our time. Bad variable names might seem okay in the start but when the code base is getting bigger with many more developers working on it, it just gets so much difficult to manage and coordinate our efforts. Our code is our creation, therefore, we ought to name it properly at the very least.


# What to do?

- ### Intention of the variable should be clear from its name.
  * If you have to write a comment to make the purpose of the variable clear, rethink the name.
  * In the old times, we had smaller (very smaller) RAMs and thus had to think very carefully while giving variables big names. But that's not an issue now, is it?. Then why give variables vague names like `a`, `n1` etc.

    consider this piece of code

    ```python
        getData(plist):
            ep = [] // expired person accounts
            for p in plist:
                if p.expiry_date >= datetime.now()
                ep.push(p)
            return ep
    ```

    and compare with

    ```python
        get_expired_persons_list(persons_list):
            expired_persons_list = []
            for person in persons_list:
                if person.expiry_date >= datetime.now()
                expired_persons_list.push(person)
            return expired_persons_list
    ```

- ### Do not use variable names which could misguide other developers.
  * An example would be naming a collection of `Person` objects `persons_list` when it is not actually a `List`. This name could misguide other developers towards thinking that it is a `List` type object. Therefore naming it `persons_collection` would be a better choice.

- ### Variable names should be meaningfully distinct
  * We often tend to name variables in a way that we could only get rid of compiler errors due to same variable names. An example would be to name variables `PersonData` and `PersonData2`. In this example, the purpose of the variables is not clear.
  * Similarly if you have written a `Person` class with methods like `get_person()`, `get_person_info()`, it would not be easy for other developers to call the correct method for the desired purpose.
A developer can easily get confused about the functionality of any of these methods.

- ### Variable names should be pronounceable.
   * Our brain can remember the variable names easily if they are pronounceable and we can avoid the unnecessary hassle of searching for the variable name in previous code to reuse it.
   An example would be using names like this:

        ```python
        class Person(object):
            ...
            get_age_year_month_days()
            get_last_modified_year_month_days()
            ...
        ```

        instead of

        ```python
        class Model01(object):
            ...
            get_ageiymd() // get age in year, month, days
            get_lmdiymd() // get last modified in year, month, days
            ...
        ```


- Searchable variables and constants should be used.
   * Suppose you have a working project. There is a requirement for every user to be at least 18 years old. You have implemented this in the following way:
        ```python
        create_person(data):
        if data['age']['year'] < 18:
            return None
        ```
  - similarly, you have used this number `18` in other places where this was required in any way.

    Now the requirement is changed and the user must be at least 15 years old to create an account. What would you do to now? Search for every occurrence of number `18` and decide if this is the number used for our purpose and then change it everywhere.

    or you could have simply used a constant `MIN_AGE_REQ=18` or `MINIMUM_AGE_REQUIREMENT=18`(which is more meaningful and searchable). Then you would have to simply change the value of this constant to satisfy the new requirement.

- ### Class names should always be nouns.
   * Class names and objects should always be nouns like `Person`, `Car`, `Record` and so on. Avoid generic and language-specific technical names like `Info`, `Manager` and `Controller`.

- ### Method names should have a verb
  * Method names should have verb or verb phrase names (preferably in the start) like `get`, `set` and `delete`.

- ### Decide a standard for names serving similar purposes.
  * Say your project has lots of classes in it with many developers working on them. You decide to use the following convention for setters and getters:

    ```python
    class PersonUpdateForm(object):
        ...
        get_age()
        set_age()
        ...
    ```

    while another developer might do things like this:

    ```python
    class UpdatePersonForm(object):
        ...
        retrive_age()
        update_age()
        ...
    ```
    This causes inconsistency in the project and difficult to search for desired objects and methods.
        Decide ahead of starting the development that what kind of conventions are you going to follow and have every developer follow those.

 - ### Use context with variable names wherever is necessary.
    * If you have a variable named `state` inside a class `MemberAddress` then it is clear that the `state` belongs to a `MemberAddress` object. but outside the class, if the variable `state` is used then it may not be clear for other developers that the variable `state` belongs to what object. In that scenario using the variable name, `member_address_state` would be more meaningful.

# Conclusion
We should never ignore the importance of naming practices as bad variable names would become a big hurdle in future development plans. Changing the codebase at that point is unnecessary overhead and can be avoided in the very beginning by following good practices.

This is all from my side for now. I will write soon about the later chapters of the book.

Happy coding!
