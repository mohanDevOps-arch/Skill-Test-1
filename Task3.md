# Task 3: Add Routes to the Flask Application

## Objective:
Enhance the existing Flask application by adding multiple routes (`/`, `/about`, `/contact`, `/status`) and verify their functionality on the EC2 instance.

---

## Prerequisite: Complete Task 2
Ensure you have completed **Task 2**, where you deployed a basic Flask application on an EC2 instance that displays "Your Name" when accessed.

---

## Step 1: Connect to the EC2 Instance

1. **Log in to your EC2 instance** using SSH:
   ```bash
   ssh -i <path-to-your-key>.pem ec2-user@<Public-IP>
   ```
   Replace `<path-to-your-key>` with the location of your `.pem` file and `<Public-IP>` with the public IP of your EC2 instance.

2. **Navigate to the Flask Application Directory**:
   If you followed Task 1, your application directory should be named `flask-app`. Navigate to it:
   ```bash
   cd ~/flask-app
   ```

---

## Step 2: Modify the `app.py` File to Add New Routes

1. **Open the `app.py` File for Editing**:
   ```bash
   nano app.py
   ```

2. **Update the Code to Include Additional Routes**:
   Replace the existing code in `app.py` with the following:
   ```python
   from flask import Flask
   app = Flask(__name__)

   # Route for Homepage
   @app.route('/')
   def home():
       return "Welcome to the Homepage! This is a basic Flask application."

   # Route for About Page
   @app.route('/about')
   def about():
       return "About Page: This application demonstrates Flask routes."

   # Route for Contact Page
   @app.route('/contact')
   def contact():
       return "Contact Page: For inquiries, contact us at support@example.com."

   # Route for Status Page
   @app.route('/status')
   def status():
       return "Status Page: The application is running smoothly!"

   if __name__ == "__main__":
       app.run(host="0.0.0.0", port=80)
   ```

3. **Save and Exit**:
   - Press `CTRL+O` to save the changes.
   - Press `Enter` to confirm.
   - Press `CTRL+X` to exit the editor.

---

## Step 3: Restart the Flask Application

1. **Stop the Existing Flask Application**:
   If the Flask application is running, stop it by pressing `CTRL+C` in the terminal or using the following command:
   ```bash
   sudo pkill -f app.py
   ```

2. **Run the Updated Flask Application**:
   ```bash
   sudo python3 app.py
   ```

3. **(Optional) Run the App in the Background Using `tmux`**:
   - If you want the app to keep running after you disconnect from SSH, use `tmux`:
     ```bash
     tmux
     sudo python3 app.py
     ```
   - Detach the session by pressing `CTRL+B`, then `D`.

---

## Step 4: Verify the Deployment

1. **Open a Web Browser**:
   - Access the following routes using the public IP of your EC2 instance:

     | Route          | URL                               | Expected Output                                              |
     |-----------------|-----------------------------------|--------------------------------------------------------------|
     | Homepage        | `http://<public-ip>/`            | "Welcome to the Homepage! This is a basic Flask application."|
     | About Page      | `http://<public-ip>/about`       | "About Page: This application demonstrates Flask routes."     |
     | Contact Page    | `http://<public-ip>/contact`     | "Contact Page: For inquiries, contact us at support@example.com."|
     | Status Page     | `http://<public-ip>/status`      | "Status Page: The application is running smoothly!"          |

   Replace `<public-ip>` with the public IP of your EC2 instance.

2. **Verify the Content**:
   - Ensure that each route displays the correct message as defined in the `app.py` file.

---

## Notes:

1. **Running Flask Without Root Privileges**:
   - If you encounter a permission error while running Flask on port 80, use an alternate port (e.g., 5000) and modify the URLs accordingly.

2. **Error Troubleshooting**:
   - If the application doesn’t run, check the Flask logs in the terminal for errors.
   - Ensure that port 80 is open in your EC2 Security Group.

3. **Future Enhancements**:
   - Add more routes to expand the functionality of your Flask app.
   - Serve dynamic content or integrate with a database.
