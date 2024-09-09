# Open-Movie-Database-API-2022

#### Introduction

This project involves the development of a movie database application utilizing Python. The program integrates with the OMDB (Open Movie Database) API to fetch movie information and leverages `pyautogui` for graphical user interface interactions. The primary goal is to create a user-friendly tool that allows users to search for movie details, manage their search history, and retrieve specific movie information.

#### Components and Setup

1. **Package Installation**

   To begin, the necessary packages are installed using `pip`:
   ```python
   import sys
   !{sys.executable} -m pip install --upgrade pip
   !{sys.executable} -m pip install omdb
   !{sys.executable} -m pip install pyautogui
   ```
   - `omdb`: A Python package for interfacing with the OMDB API to retrieve movie data.
   - `pyautogui`: A package used to create GUI interactions like alerts and prompts, which reduces manual input errors.

2. **API Key Configuration**

   Access to the OMDB API requires an API key. This key is set up using the following function:
   ```python
   import omdb

   def APIkey(key):
       omdb.set_default('apikey', key)
   ```

3. **Text File Management**

   - **Splitting History**: The `split` function processes a text file named "History.txt" to organize movie data by a specified delimiter, such as "title".
     ```python
     def split(info):
         file_opener = open("History.txt", "r")
         unsorted_history = file_opener.read()
         file_opener.close()
         information = unsorted_history.split(info)
         return information
     ```

   - **Clearing History**: The `clear` function empties the contents of the specified text file.
     ```python
     def clear(textfile):
         open(textfile, "w").close()
     ```

4. **Search Functionality**

   - **Movie Search**: The `search` function allows users to find information about a movie. It first checks if the movie is already in the "History.txt" file. If not, it fetches data from OMDB and updates the history.
     ```python
     import json
     import pyautogui

     def search(movietitle):
         movietitle = movietitle.lower()
         count = 0
         check = True
         information = split("title")
         for word in information:
             if movietitle in word.lower():
                 count += 1
                 if count <= 1:
                     pyautogui.alert(text="{\"title"+word[:-2], title="Movie Database LTD.", button="continue")
                     check = False
         
         if check:
             APIkey('62c6d5ed')
             name = omdb.get(title=movietitle)
             if len(name) > 0:
                 history_write(name)
                 pyautogui.alert(text=name, title="Movie Database LTD.", button="continue")
             else:
                 pyautogui.alert(text=movietitle + " does not exist!", title="Movie Database LTD.", button="continue")
     ```

5. **Detailed Movie Information**

   - **Specific Search**: The `specific_search` function retrieves specific details about a movie, such as the title or poster. It also handles cases where the movie is not found.
     ```python
     import webbrowser

     def specific_search(movietitle, information):
         movietitle = movietitle.lower()
         check = True
         APIkey('62c6d5ed')
         name = omdb.get(title=movietitle)
         if len(name) > 1:
             if information == "title":
                 pyautogui.alert(text="Title: " + name["title"], title="Movie Database LTD.", button="continue")
                 information = split(",")
                 for word in information:
                     if movietitle in word.lower():
                         check = False
                 if check:
                     history_write(name)
             elif information == "poster":
                 webbrowser.open(str(name[information]))
                 information = split(",")
                 for word in information:
                     if movietitle in word.lower():
                         check = False
                 if check:
                     history_write(name)
             else:
                 pyautogui.alert(text="Title: " + movietitle + "\n" +information + ": " + str(name[information]), title="Movie Database LTD.", button="continue")
                 information = split(",")
                 for word in information:
                     if movietitle in word.lower():
                         check = False
                 if check:
                     history_write(name)
         else:
             pyautogui.alert(text=movietitle + " does not exist!", title="Movie Database LTD.", button="continue")
     ```

6. **Search History Management**

   - **Viewing History**: The `history` function displays the titles of previously searched movies.
     ```python
     def history():
         check = False
         history_store = ""
         information = split(",")
         if len(information) > 1:
             for word in information:
                 if "title" in word:
                     word = word.replace("{","")
                     history_store = history_store + "\n" + word
                     check = True
         else:
             pyautogui.alert(text="You haven't searched anything yet!", title="Movie Database LTD.", button="continue")
         
         if check:
             pyautogui.alert(text= history_store, title="Movie Database LTD.", button="continue")
     ```

7. **User Interaction**

   - **Main Method**: The main method guides users through a series of options:
     ```python
     import pyautogui

     new_user = pyautogui.confirm("Welcome to Movie Database LTD. Are you a new user?", title = "Movie Database LTD.", buttons = ["Yes","No"])
     if new_user == "No":
         file_opener = open("Password.txt", "r")
         unsorted_history = file_opener.read()
         file_opener.close()
         if len(unsorted_history) < 1 or unsorted_history == "Default":
             password = "Default"
             clear("Password.txt")
     if new_user == "Yes":
         clear("History.txt")
         clear("Password.txt")
         while True:
             password = pyautogui.password(text="Enter a password to store history (minimum length = 6)", title="Movie Database LTD.", default='', mask="*")
             if password == None:
                 pyautogui.alert(text="Thank you for using Movie Database LTD.", title="Movie Database LTD.", button="Exit")
                 sys.exit("You pressed cancel")
             else:
                 if password == '':
                     pyautogui.alert(text="No password entered, please try again!", title="Movie Database LTD.", button="Try Again.")
                 elif len(password) < 6:
                     pyautogui.alert(text="Password too short, please try again!", title="Movie Database LTD.", button="Try Again.")
                 else:
                     password_write(password)
                     break
     
     while True:
         options = pyautogui.confirm("Please select an option.\nA: Find information on a movie\nB: Clear search history\nC: Show search history\nD: Search specific information about a movie.\nE: Exit Program.", title = "Movie Database LTD.",buttons = ["A","B","C","D","E"])
         if options == "A":
             movietitle = pyautogui.prompt("Enter the title of the movie you want to find", title = "Movie Database LTD.")
             if movietitle == None:
                 pyautogui.alert("You pressed cancel.", title = "Movie Database LTD.", button = "continue")
             else:
                 search(movietitle.strip())
         elif options == "B":
             password_check = pyautogui.password(text="Enter your password to clear history", title="Movie Database LTD.", default='', mask="*")
             if password == password_check:
                 clear("History.txt")
             else:
                 pyautogui.alert(text="Incorrect password entered!", title="Movie Database LTD.", button="OK")
         elif options == "C":
             password_check = pyautogui.password(text="Enter your password to view history", title="Movie Database LTD.", default='', mask="*")
             if password == password_check:
                 history()
             else:
                 pyautogui.alert(text="Incorrect password entered!", title="Movie Database LTD.", button="OK")
         elif options == "D":
             movietitle = pyautogui.prompt(text="Enter the title of the movie you want to find",title="Movie Database LTD.")
             if movietitle == None:
                 pyautogui.alert(text="You pressed cancel", title="Movie Database LTD.", button="continue")
             else:
                 information = pyautogui.confirm("Enter the information you want to retrieve from "+ movietitle, title="Movie Database LTD.", buttons = ['title','year','rated','released','runtime','genre','director','writer','actors','plot','language','awards','poster','ratings','metascore','imdb_rating','imdb_votes','imdb_id','type','dvd','box_office','production','website','response'])
                 specific_search(movietitle.strip(),information)
         elif options == "E":
             break

     pyautogui.alert(text="Thank you for using Movie Database LTD!", title="Movie Database LTD.", button="Exit")
     ```

#### Testing and Validation

To ensure the robustness of the program, it is tested using various types of data:
- **Normal Data**: Typical inputs such as valid movie titles and passwords are used to verify expected behavior

.
- **Boundary Cases**: Cases involving empty strings and short passwords are tested to check input validation.
- **Error Handling**: Invalid movie titles and incorrect passwords are tested to ensure proper error messages and handling.

#### Conclusion

This movie database application provides a comprehensive tool for users to search for movie information, manage search history, and retrieve specific movie details. It incorporates user-friendly GUI elements and robust error handling to enhance usability and reliability.
