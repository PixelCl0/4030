<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Work Time Tracker</title>
    <script src="https://cdn.emailjs.com/dist/email.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f4f4f9;
        }
        .container {
            max-width: 500px;
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            margin-bottom: 20px;
        }
        form {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }
        label {
            font-weight: bold;
        }
        input, select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            padding: 10px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Work Time Tracker</h1>
        <form id="work-time-form">
            <label for="task-name">Task Name *</label>
            <input type="text" id="task-name" name="task-name" required>

            <label for="user">Assigned To *</label>
            <select id="user" name="user" required>
                <option value="">-- Select --</option>
                <option value="Tristan">Tristan</option>
                <option value="Chloé">Chloé</option>
            </select>

            <label for="ri">RI (6 digits) *</label>
            <input type="text" id="ri" name="ri" pattern="\d{6}" title="Enter a 6-digit number" required>

            <label for="comments">Comments</label>
            <textarea id="comments" name="comments"></textarea>

            <fieldset>
                <legend>Time Input</legend>
                <label for="start-time">Start Time</label>
                <input type="time" id="start-time" name="start-time">

                <label for="end-time">End Time</label>
                <input type="time" id="end-time" name="end-time">

                <label for="duration">Duration (HH:MM)</label>
                <input type="text" id="duration" name="duration" pattern="\d{1,2}:\d{2}" title="Enter duration as HH:MM">

                <button type="button" id="start-btn">Start Task</button>
                <button type="button" id="stop-btn" disabled>Stop Task</button>
            </fieldset>

            <button type="submit">Submit</button>
        </form>
    </div>

    <script>
        (function() {
            emailjs.init('oxJ_O0CNx5-vHltSs');
        })();

        let startTime;
        document.getElementById('start-btn').addEventListener('click', function() {
            startTime = new Date();
            document.getElementById('start-btn').disabled = true;
            document.getElementById('stop-btn').disabled = false;
        });

        document.getElementById('stop-btn').addEventListener('click', function() {
            const endTime = new Date();
            const duration = Math.round((endTime - startTime) / 60000); // duration in minutes
            const hours = Math.floor(duration / 60);
            const minutes = duration % 60;
            document.getElementById('duration').value = `${hours}:${minutes < 10 ? '0' + minutes : minutes}`;
            document.getElementById('start-btn').disabled = false;
            document.getElementById('stop-btn').disabled = true;
        });

        document.getElementById('work-time-form').addEventListener('submit', function(event) {
            event.preventDefault();

            const formData = {
                task_name: document.getElementById('task-name').value,
                user: document.getElementById('user').value,
                ri: document.getElementById('ri').value,
                comments: document.getElementById('comments').value,
                start_time: document.getElementById('start-time').value,
                end_time: document.getElementById('end-time').value,
                duration: document.getElementById('duration').value,
                timestamp: new Date().toLocaleString(),
            };

            emailjs.send('Worktime tracker', 'template_41mdyzu', formData)
                .then(function(response) {
                    console.log('SUCCESS!', response.status, response.text);
                    alert('Task recorded successfully and sent via email!');
                }, function(error) {
                    console.log('FAILED...', error);
                    alert('Failed to send task details. Please try again.');
                });

            this.reset();
        });
    </script>
</body>
</html>
