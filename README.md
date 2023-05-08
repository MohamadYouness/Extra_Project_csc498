# Extra_Project_csc498

Note 1: the project was not finished, I was stuck on a critical step and I could not solve it within a short time
Note 2: AI was used in this project in some places 
import sys
import csv
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By


def main(username, password):
    # Set up the web driver
    driver = webdriver.Chrome()

    # Navigate to the login page
    driver.get("https://lau.edu.lb/login")

    # Log in
    try:
        WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.NAME, "username")))
    except TimeoutException:
        print("Failed to load login page")
        sys.exit(1)

    username_field = driver.find_element_by_name("username")
    password_field = driver.find_element_by_name("password")
    username_field.send_keys(username)
    password_field.send_keys(password)
    password_field.send_keys(Keys.RETURN)

    driver.get("https://banweb.lau.edu.lb/")
    driver.get("https://banweb.lau.edu.lb/prod/twbkwbis.P_GenMenu?name=bmenu.P_StuMainMnu")
    driver.get("https://banweb.lau.edu.lb/prod/twbkwbis.P_GenMenu?name=bmenu.P_RegMnu")
    driver.get("https://banweb.lau.edu.lb/prod/bwskfcls.p_sel_crse_search")
   # from selenium.webdriver.support.ui import Select

   #  # locate the select element
   #  select = Select(driver.find_element_by_id('semester-select'))

   #  # select by visible text
   #  select.select_by_visible_text('Fall 2023')

   #  # Wait for the login to complete
    try:
        WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, "https://banweb.lau.edu.lb/prod/bwskfcls.P_GetCrse")))
    except TimeoutException:
        print("Failed to log in")
        sys.exit(1)

    # Navigate to the course offerings page
    driver.find_element_by_id("https://banweb.lau.edu.lb/prod/bwskfcls.P_GetCrse").click()

    # Extract course information
    try:
        WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CSS_SELECTOR, ".course_row")))
    except TimeoutException:
        print("Failed to load course offerings page")
        sys.exit(1)

    courses = driver.find_elements_by_css_selector(".course_row")
    course_data = []

    for course in courses:
        crn = course.find_element_by_css_selector(".crn").text
        subject_name = course.find_element_by_css_selector(".subject_name").text
        campus = course.find_element_by_css_selector(".campus").text
        credits = course.find_element_by_css_selector(".credits").text
        time = course.find_element_by_css_selector(".time").text
        instructor = course.find_element_by_css_selector(".instructor").text
        remaining_seats = course.find_element_by_css_selector(".remaining_seats").text
        date = course.find_element_by_css_selector(".date").text
        location = course.find_element_by_css_selector(".location").text
        attribute = course.find_element_by_css_selector(".attribute").text

        course_data.append([crn, subject_name, campus, credits, time, instructor, remaining_seats, date, location, attribute])

    # Save the data to a CSV file
    with open("course_offerings.csv", "w", newline="") as csvfile:
        writer = csv.writer(csvfile)
        writer.writerow(["CRN", "Subject Name", "Campus", "Credits", "Time", "Instructor", "Remaining Seats", "Date", "Location", "Attribute"])
        writer.writerows(course_data)

    # Close the web driver
    driver.quit()


if __name__ == "__main__":
    if len(sys.argv) != 3:
        print("Usage: python script.py <username> <password>")
        sys.exit(1)

    main(sys.argv[1], sys.argv[2])
