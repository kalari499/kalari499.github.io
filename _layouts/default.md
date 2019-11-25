<!DOCTYPE html>
<html>
	<head>
        <link rel="stylesheet" type="text/css" href="/css/bootstrap.min.css">
        <link rel="stylesheet" type="text/css" href="/css/main.css">
        <title>{{ page.title }}</title>
	</head>
	<body>
        
        <div class="column col-sm-3" id="sidebar">
            <img class='img-circle' src="/assets/avatar.png">
            <p>kalari499's Blog</p>
            <ul class="nav">
                <li><a href="/">Home</a></li>
                <li><a href="/about">About Me</a></li>
                <li><a href="/schedule">Blog Schedule</a></li>
                <li><a href="/Incredibles">Project Incredibles</a></li>
                <li><a href="/MonstersInc">Project MonstersInc</a></li>
                <li><a href="/archive">Archive</a></li>
            </ul>
        </div>

		<div class="column col-sm-9" id="main">
            {{ content }}
		</div>

		<footer class="column col-sm-9" id="footer">
            <p class="text-muted">Made by kalari499 &#169; 2019</p>
		</footer>
	</body>
</html>
