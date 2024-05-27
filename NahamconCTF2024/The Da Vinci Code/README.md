<br>
<img width="969" alt="Screenshot 2024-05-25 at 20 27 06" src="https://github-production-user-asset-6210df.s3.amazonaws.com/45916763/333977252-7c562a19-f71f-4a15-b142-c3e5759606ab.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240527%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240527T052646Z&X-Amz-Expires=300&X-Amz-Signature=78a9a0fd2e21d5a4bf5ec85d74693f82e31ecc37d3b3b249789269a0e6e729fb&X-Amz-SignedHeaders=host&actor_id=45916763&key_id=0&repo_id=661637344">
<br>
The Challenge provided us with an instance where we have https://challenge.nahamcon.com:3167/, and we have a webpage where it says 'Explore the mystery.' Also, it has a button that says 'Learn about the code,' and it links to /code.
<br>
<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/c12bb22e-8ba1-4eac-b898-0468796a20ef">
<br>
I got stuck where I thought it was a Werkzeug debug RCE, and I tried to exploit it, but with no luck.
<br>
<br>
<img width="696" alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/c85fd594-cd49-4fbc-ae0f-cccd7a7d4eba">
<br>
<br>
Then, my friend gave me a hint where the solution is in the error code. So, I analyzed the error code one by one, and this particular error caught my attention.
<br>
<img  alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/4f01af27-0374-467b-bbd2-f9d0ad0d873a">
<br>
<br>
It had two request methods, GET and PROPFIND. PROPFIND is used to retrieve properties stored as XML from a web resource. It is also overloaded to allow one to retrieve the collection structure (a.k.a. directory hierarchy) of a remote system.

I tried to manipulate the request of the root directory (/) through Burp Suite. I changed the request method from GET to PROPFIND, and boom! It gave me a result with multiple directories leaked.
<br>
<img  alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/81c09c49-101a-423a-89fe-f3cd2cb20973">
<br>
I saw the directory /the_secret_dav_inci_code/flag.txt, which contains the flag. However, the problem was that the directory /the_secret_dav_inci_code, which contains flag.txt, isn't in the web root directory. I needed to find a way to access the directory that contains the flag.
<br>
<img  alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/e07965e7-9c21-44e4-afb7-88b6c0b69b94">
<br>
I did a little bit of digging, and I found the app.py.backup in static directory, which contains the Flask web application code.
<br>
<img  alt="dsadsa" src="https://github.com/er4pwn/CTF_writeup/assets/45916763/74c90c13-8b2d-4724-801a-0590127846e1">
<br>
<br>
```
@app.route('/<path:path>', methods=['GET', 'PROPFIND', 'MOVE'])
def handle_webdav(path):
    full_path = os.path.join(os.getcwd(), path)
    if request.method == 'PROPFIND':
        if os.path.exists(full_path):
            properties = {'message': 'WebDAVinci Code'}
            children = None 
            if os.path.isdir(full_path):
                properties['directory'] = 'True'
                children = os.listdir(full_path)
            else:
                properties['file'] = 'True'
            xml_response = create_webdav_response('/' + path, properties, children)
            return Response(xml_response, mimetype='application/xml')
        abort(404)
    elif request.method == 'MOVE':
        destination = request.headers.get('Destination')
        if destination:
            destination_path = os.path.join(os.getcwd(), destination.strip('/'))
            os.makedirs(os.path.dirname(destination_path), exist_ok=True)
            shutil.move(full_path, destination_path)
            return Response(status=204)
        abort(405)
    abort(404)
```

