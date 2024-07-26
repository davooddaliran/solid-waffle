# solid-waffle
from flask import Flask, render_template, request, redirect, url_for, flash
from cs50 import SQL
from flask_mail import Mail, Message
import random
import string

app = Flask(__name__)

# Configure CS50 Library to use SQLite database
db = SQL("sqlite:///users.db")

# Configure Flask-Mail
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 465
app.config['MAIL_USERNAME'] = 'your-email@gmail.com'
app.config['MAIL_PASSWORD'] = 'your-email-password'
app.config['MAIL_USE_TLS'] = False
app.config['MAIL_USE_SSL'] = True

mail = Mail(app)

# Secret key for session management
app.secret_key = 'supersecretkey'

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/register', methods=['POST'])
def register():
    email = request.form.get('email')
    
    if not email:
        flash('Email is required!')
        return redirect(url_for('index'))
    
    # Generate a random verification code
    verification_code = ''.join(random.choices(string.ascii_uppercase + string.digits, k=6))
    
    # Store user and verification code in database
    db.execute("INSERT INTO users (email, verification_code) VALUES (?, ?)", email, verification_code)
    
    # Send verification email
    msg = Message('Your Verification Code', sender='your-email@gmail.com', recipients=[email])
    msg.body = f'Your verification code is: {verification_code}'
    mail.send(msg)
    
    flash('A verification code has been sent to your email.')
    return redirect(url_for('index'))

if __name__ == '__main__':
    app.run(debug=True)
    
ببین با زبان برنامه نویسی cs50 میخوام یه کد بنویسی که بتون تو طراحی یه صفحه وب سایتم کسی که میاد ثبت نام میکنه یه کد به ایمیلش بره 
ChatGPT
برای این کار نیاز دارید از زبان‌های مختلف برنامه‌نویسی برای طراحی وب‌سایت و ارسال ایمیل استفاده کنید. با استفاده از CS50 و Flask (یک میکروفریم‌ورک پایتون برای توسعه وب)، شما می‌توانید یک سیستم ثبت‌نام ساده را پیاده‌سازی کنید که به کاربران کدی به ایمیل‌شان ارسال می‌کند.

ابتدا باید CS50 و Flask را نصب کنید:

bash
Copy code
pip install cs50 Flask Flask-Mail
سپس کد زیر را ایجاد کنید:

app.py
python
Copy code
from flask import Flask, render_template, request, redirect, url_for, flash
from cs50 import SQL
from flask_mail import Mail, Message
import random
import string

app = Flask(__name__)

# Configure CS50 Library to use SQLite database
db = SQL("sqlite:///users.db")

# Configure Flask-Mail
app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 465
app.config['MAIL_USERNAME'] = 'your-email@gmail.com'
app.config['MAIL_PASSWORD'] = 'your-email-password'
app.config['MAIL_USE_TLS'] = False
app.config['MAIL_USE_SSL'] = True

mail = Mail(app)

# Secret key for session management
app.secret_key = 'supersecretkey'

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/register', methods=['POST'])
def register():
    email = request.form.get('email')
    
    if not email:
        flash('Email is required!')
        return redirect(url_for('index'))
    
    # Generate a random verification code
    verification_code = ''.join(random.choices(string.ascii_uppercase + string.digits, k=6))
    
    # Store user and verification code in database
    db.execute("INSERT INTO users (email, verification_code) VALUES (?, ?)", email, verification_code)
    
    # Send verification email
    msg = Message('Your Verification Code', sender='your-email@gmail.com', recipients=[email])
    msg.body = f'Your verification code is: {verification_code}'
    mail.send(msg)
    
    flash('A verification code has been sent to your email.')
    return redirect(url_for('index'))

if __name__ == '__main__':
    app.run(debug=True)
templates/index.html
html
Copy code
<!DOCTYPE html>
<html>
<head>
    <title>Register</title>
</head>
<body>
    <h1>Register</h1>
    <form action="/register" method="post">
        <label for="email">Email:</label>
        <input type="email" id="email" name="email">
        <button type="submit">Register</button>
    </form>
    {% with messages = get_flashed_messages() %}
        {% if messages %}
            <ul>
                {% for message in messages %}
                    <li>{{ message }}</li>
                {% endfor %}
            </ul>
        {% endif %}
    {% endwith %}
</body>
</html>
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    email TEXT NOT NULL,
    verification_code TEXT NOT NULL
);
