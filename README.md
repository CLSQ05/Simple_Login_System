<?php
session_start();

if (!isset($_SESSION['logged_in'])) {
    header('Location: index.php');
    exit();
}

$username = $_SESSION['username'];
?>

<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600&display=swap');

        body {
            margin: 0;
            padding: 0;
            font-family: 'Poppins', sans-serif;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #000000da, #1e023dff, #552789ff, #8458ddff, #cb82ffff);
            background-size: 400% 400%;
            animation: gradientMove 20s ease infinite;
            overflow: hidden;
        }

        .wrapper {
            width: 100%;
            max-width: 600px;
            padding: 25px 20px;
            border-radius: 20px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            box-shadow: 0 0 25px rgba(0, 0, 0, 0.35);
            text-align: center;
            animation: fadeIn 0.8s ease;
            box-sizing: border-box;
        }

        h2 {
            color: white;
            font-weight: 600;
            margin-top: 5px;
            margin-bottom: 25px;
            font-size: 1.6rem;
        }

        hr {
            margin-bottom: 30px;
        }

        p {
            color: white;
            font-size: 1rem;
        }

        button {
            width: 100%;
            padding: 12px;
            margin-top: 12px;
            border: none;
            border-radius: 10px;
            background: #00a86b;
            color: white;
            font-size: 16px;
            cursor: pointer;
            transition: 0.3s;
            box-sizing: border-box;
        }

        button:hover {
            box-shadow: 0 0 15px #b37bff;
            transform: scale(1.03);
        }
    </style>
</head>

<body>
    <div class="wrapper">
        <h2>✨ WELCOME, <?php echo $username; ?>! ✨</h2>
        <hr>
        <p><b>You are now logged in 💖</b></p>
        <form action="logout.php" method="POST">
            <button type="submit"><b>Logout</b></button>
        </form>
    </div>

    <?php
    if (isset($_SESSION['message'])) {
        echo "<div class='alert alert-success'>" . $_SESSION['message'] . "</div>";
        unset($_SESSION['message']);
    }
    ?>
</body>
</html>

<!-- ============================= -->
<!-- LOGIN PAGE                    -->
<!-- ============================= -->

<?php session_start(); ?>
<!DOCTYPE html>
<html>

<head>
    <title>Login</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>

<body>
    <div class="wrapper">
        <h2>🍁== LOGIN ==🍁</h2>
        <hr>
        <form action="login.php" method="POST">
            <div class="inputBox"><input type="text" name="username" placeholder="Username" required></div>
            <div class="inputBox"><input type="password" name="password" placeholder="Password" required></div>
            <button type="submit"><b>Login</b></button>
        </form>
        <br>
        <a href="register.php" style="color:white; text-decoration:underline;"><b>Register Here</b></a>
    </div>

    <?php
    if (isset($_SESSION['message'])) {
        $type = 'alert-success';
        if (isset($_SESSION['invalid_login'])) {
            $type = 'alert-error';
        }
        if (isset($_SESSION['logout_alert'])) {
            $type = 'alert-logout';
        }
        echo "<div class='alert $type'>" . $_SESSION['message'] . "</div>";
        unset($_SESSION['message']);
        unset($_SESSION['invalid_login']);
        unset($_SESSION['logout_alert']);
    }
    ?>
</body>

</html>

<!-- ============================= -->
<!-- REGISTER PAGE                 -->
<!-- ============================= -->

<?php session_start(); ?>
<!DOCTYPE html>
<html>

<head>
    <title>Register</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>

<body>
    <div class="wrapper">
        <h2>🌸== REGISTER ==🌸</h2>
        <hr>
        <form action="save_register.php" method="POST">
            <div class="inputBox"><input type="text" name="username" placeholder="Create Username" required></div>
            <div class="inputBox"><input type="password" name="password" placeholder="Create Password" required></div>
            <button type="submit"><b>Register</b></button>
        </form>
        <br>
        <a href="index.php" style="color:white; text-decoration:underline;"><b>Back to Login</b></a>
    </div>

    <?php
    if (isset($_SESSION['message'])) {
        $type = isset($_SESSION['invalid_login']) ? 'alert-error' : 'alert-success';
        echo "<div class='alert $type'>" . $_SESSION['message'] . "</div>";
        unset($_SESSION['message']);
        unset($_SESSION['invalid_login']);
    }
    ?>
</body>

</html>

<!-- ============================= -->
<!-- SAVE REGISTER LOGIC           -->
<!-- ============================= -->

<?php
session_start();

$username = trim($_POST['username']);
$password = trim($_POST['password']);

$file = "users.txt";

if (!file_exists($file)) {
    file_put_contents($file, "");
}

$users = file($file, FILE_IGNORE_NEW_LINES);

foreach ($users as $user) {
    list($u, $p) = explode(":", $user);
    if ($u === $username) {
        $_SESSION['message'] = "Username already exists!";
        $_SESSION['invalid_login'] = true;
        header("Location: register.php");
        exit();
    }
}

file_put_contents($file, "$username:$password\n", FILE_APPEND);

$_SESSION['message'] = "Registration successful!";
header("Location: index.php");
exit();
?>

<!-- ============================= -->
<!-- LOGIN LOGIC                   -->
<!-- ============================= -->

<?php
session_start();

$username = trim($_POST['username']);
$password = trim($_POST['password']);

$file = 'users.txt';

if (!file_exists($file)) {
    $_SESSION['message'] = 'No users registered.';
    $_SESSION['invalid_login'] = true;
    header('Location: index.php');
    exit();
}

$valid = false;
$lines = file($file, FILE_IGNORE_NEW_LINES);

foreach ($lines as $line) {
    list($savedUser, $savedPass) = explode(':', $line);

    if ($savedUser === $username && $savedPass === $password) {
        $valid = true;
        break;
    }
}

if ($valid) {
    $_SESSION['logged_in'] = true;
    $_SESSION['username'] = $username;
    $_SESSION['message'] = "Welcome, $username!";
    header('Location: homepage.php');
} else {
    $_SESSION['message'] = 'Invalid username or password!';
    $_SESSION['invalid_login'] = true;
    header('Location: index.php');
}
exit();
?>

<!-- ============================= -->
<!-- LOGOUT LOGIC                  -->
<!-- ============================= -->

<?php
session_start();
session_destroy();

session_start();
$_SESSION['message'] = "You have been logged out!";
$_SESSION['logout_alert'] = true;

header("Location: index.php");
exit();
?>
