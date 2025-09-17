# Description:

I've heard so much about SQL injection, so I've used parameterized queries here! Using the endpoint /source to view the source code for easier to exploit the bugs

# Solutions:

= `/source` có code như sau:

```
#!/usr/bin/env python3

from sqlite3 import *
from os import system
from flask import Flask, request, Response
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
from flask import render_template

app = Flask(__name__)

limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["50000 per hour"],
    storage_uri="memory://",
)


@app.route('/', methods=["GET"])
@limiter.limit("5/second")
def index():
    if 'lastname' not in request.args:
        return render_template("index.html")
    else:
        con = connect("users.db")
        cur = con.cursor()
        res = cur.execute("SELECT * from authors where last LIKE ?",
                          (request.args['lastname'].replace("%", ""),))
        # Fetch all rows from the result set
        rows = res.fetchall()  
        return render_template("index.html", rows=rows)


@app.route('/source')
@limiter.limit("5/second")
def source():
    return Response(open(__file__).read(), mimetype="text/plain")


if __name__ == "__main__":
    system("rm -f users.db")
    con = connect("users.db")
    cur = con.cursor()
    cur.execute('''
        CREATE TABLE authors(first, middle, last)
    ''')
    cur.execute(f'''
        INSERT INTO authors VALUES
            ("Tobias", '', "Smollett"),
            ("William", '', "Shakespeare"),
            ("Edward", "Morgan", "Forster"),
            ("George", "", "Eliot"),
            ("Louisa", "May", "Alcott"),
            ("Lucy", "Maud", "Montgomery"),
            ("Frank", "T.", "Merill"),
            ("Herman", "", "Melville"),
            ("Alexandre", "", "Dumas"),
            ("Elizabeth", "", "Von Arnim"),
            ("Pseudonymous", "Unpuzzler7", "{open("/flag.txt").read()}")
    ''')
    con.commit()
    cur.close()
    con.close()

    app.run('0.0.0.0', 1337)
```
