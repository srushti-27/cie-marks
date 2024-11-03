from twilio.rest import Client

# Twilio credentials
account_sid = 'AC6aece603273ef0d8d5bc1384df5508f0'  # Replace with your Twilio account SID
auth_token = '3fe96c378dcbde17a442915f09132407'  # Replace with your Twilio auth token
client = Client(account_sid, auth_token)

# Google Form link
google_form_link = 'https://docs.google.com/forms/d/e/1FAIpQLSevrf6MCrjT86VkPumVybtT2DPbjj5TmNM8sZx-WW2LkgQEuQ/viewform?usp=sf_link'

# List to store student details
students = []
total_cie_sum = 0  # Variable to store the sum of all CIE marks

while True:
    name = input("Enter student name: ")
    roll_number = input("Enter roll number: ")
    phone_number = input("Enter phone number (format: +91XXXXXXXXXX): ")

    # Validate phone number format
    if not phone_number.startswith('+') or len(phone_number) < 13:
        print("Invalid phone number format. Please include '+' and use the correct format.")
        continue  # Ask for input again

    try:
        # Input marks
        ia1 = int(input("First IA marks (max 30): "))
        ia2 = int(input("Second IA marks (max 30): "))
        ia3 = int(input("Third IA marks (max 30): "))
        skill_test1 = int(input("Skill Test 1 marks (max 20): "))
        skill_test2 = int(input("Skill Test 2 marks (max 20): "))
        record_marks = int(input("Record Marks (max 10): "))

        # Calculate totals
        ia_total = ia1 + ia2 + ia3
        skill_test_total = skill_test1 + skill_test2
        ia_total_divided = ia_total / 3
        skill_test_total_divided = skill_test_total / 2

        cie_total = ia_total_divided + skill_test_total_divided + record_marks

        # Append student details
        students.append({
            "name": name,
            "roll_number": roll_number,
            "phone_number": phone_number,
            "cie_total": cie_total
        })
        total_cie_sum += cie_total  # Update total CIE marks

        # Send SMS with CIE marks and Google Form link
        message = client.messages.create(
            body=f"Hello {name}, your CIE total marks are {round(cie_total, 2)} out of 60. "
                 f"Please provide your feedback here: {google_form_link}",
            from_='+18059198788',  # Replace with your Twilio phone number
            to=phone_number
        )
        print(f"Message sent to {name} at {phone_number}")

    except Exception as e:
        print(f"An error occurred while sending the message: {e}")

    cont = input("If you want to enter another student's details? (Y/N): ").lower()
    if cont != 'y':
        break

# Display the results
print("\nOutput:")
for student in students:
    print(f"Student Name: {student['name']}")
    print(f"Roll Number: {student['roll_number']}")
    print(f"Phone Number: {student['phone_number']}")
    print(f"Total CIE Marks (out of 60): {round(student['cie_total'], 2)}\n")

# Display the total sum of CIE marks
print(f"Total Sum of CIE Marks for all students: {round(total_cie_sum, 2)}")
