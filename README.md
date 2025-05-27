# Attendance-app
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>On Attendance App</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap');
  body {
    margin: 0;
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #667eea, #764ba2);
    color: #fff;
    display: flex;
    flex-direction: column;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
  }
  header {
    text-align: center;
    margin-bottom: 20px;
  }
  header h1 {
    font-weight: 600;
    font-size: 2.8rem;
    margin-bottom: 5px;
  }
  header p {
    font-weight: 400;
    color: #ddd;
  }
  .container {
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 12px;
    padding: 25px 30px;
    width: 100%;
    max-width: 700px;
    box-shadow: 0 15px 25px rgba(0,0,0,0.3);
  }
  .date-picker {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
  }
  .date-picker label {
    font-weight: 600;
  }
  #datetime-display {
    text-align: right;
    font-weight: 600;
    margin-bottom: 20px;
    font-size: 0.95rem;
    color: #ddd;
  }
  .students-list {
    max-height: 400px;
    overflow-y: auto;
    margin-bottom: 30px;
  }
  .student-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: rgba(255, 255, 255, 0.15);
    padding: 12px 18px;
    border-radius: 8px;
    margin-bottom: 10px;
    transition: background-color 0.3s ease;
  }
  .student-item.present {
    background-color: rgba(34, 197, 94, 0.8);
  }
  .student-item.absent {
    background-color: rgba(239, 68, 68, 0.8);
  }
  .student-info {
    display: flex;
    flex-direction: column;
    user-select: none;
  }
  .student-name {
    font-size: 1rem;
    font-weight: 600;
  }
  .student-enrollment {
    font-size: 0.85rem;
    opacity: 0.75;
    margin-top: 2px;
  }
  .attendance-toggle {
    display: flex;
    gap: 15px;
  }
  .attendance-toggle label {
    cursor: pointer;
    font-weight: 600;
    user-select: none;
  }
  input[type="radio"] {
    accent-color: #fff;
    cursor: pointer;
  }
  .buttons {
    text-align: center;
    margin-bottom: 20px;
  }
  button {
    background: #6c63ff;
    border: none;
    padding: 12px 30px;
    margin: 0 12px;
    border-radius: 25px;
    color: #fff;
    font-weight: 600;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s ease;
  }
  button:hover {
    background: #574fd6;
  }
  footer {
    margin-top: 30px;
    color: #ccc;
    font-size: 0.9rem;
  }
  /* Scrollbar Styling */
  ::-webkit-scrollbar {
    width: 8px;
  }
  ::-webkit-scrollbar-track {
    background: rgba(255,255,255,0.1);
    border-radius: 8px;
  }
  ::-webkit-scrollbar-thumb {
    background: #6c63ff;
    border-radius: 8px;
  }
  /* Attendance Summary Table */
  #attendance-summary {
    margin-top: 10px;
    border-collapse: collapse;
    width: 100%;
    color: #eee;
  }
  #attendance-summary th,
  #attendance-summary td {
    border: 1px solid #555;
    padding: 8px 12px;
    text-align: left;
  }
  #attendance-summary th {
    background-color: #574fd6;
    font-weight: 600;
  }
  #attendance-summary tbody tr:nth-child(even) {
    background-color: rgba(255,255,255,0.05);
  }
  #attendance-summary tbody tr.present {
    background-color: rgba(34,197,94,0.3);
  }
  #attendance-summary tbody tr.absent {
    background-color: rgba(239,68,68,0.3);
  }
</style>
</head>
<body>
  <header>
    <h1>On Attendance App</h1>
    <p>Mark and view student attendance</p>
  </header>

  <div class="container" role="main">
    <div class="date-picker">
      <label for="attendance-date">Select Date:</label>
      <input type="date" id="attendance-date" aria-label="Attendance Date" />
    </div>

    <div id="datetime-display" aria-live="polite" aria-atomic="true">
      Date: <span id="display-date"></span> | Time: <span id="display-time"></span>
    </div>

    <div class="students-list" id="students-list" aria-live="polite" aria-relevant="additions">
      <!-- Students attendance items will appear here -->
    </div>

    <div class="buttons">
      <button id="save-attendance" aria-label="Save attendance for selected date">Save Attendance</button>
      <button id="reset-attendance" aria-label="Reset attendance for selected date">Reset Attendance</button>
    </div>

    <section aria-label="Attendance Summary">
      <h2>Attendance Summary</h2>
      <table id="attendance-summary" role="table" aria-describedby="summary-desc">
        <caption id="summary-desc" class="sr-only">Summary of attendance records showing name, enrollment id, status, date, and time</caption>
        <thead>
          <tr>
            <th>Name</th>
            <th>Enrollment ID</th>
            <th>Status</th>
            <th>Date</th>
            <th>Time</th>
          </tr>
        </thead>
        <tbody>
          <!-- Attendance summary rows -->
        </tbody>
      </table>
    </section>
  </div>

  <footer>
    &copy; 2024 On Attendance App
  </footer>

<script>
  // Sample BCA students list with enrollment IDs
  const students = [
    { name: "Aarav Sharma", enrollmentId: "BCA2024001" },
    { name: "Anaya Patel", enrollmentId: "BCA2024002" },
    { name: "Rohan Mehta", enrollmentId: "BCA2024003" },
    { name: "Simran Kaur", enrollmentId: "BCA2024004" },
    { name: "Aditya Singh", enrollmentId: "BCA2024005" },
    { name: "Neha Gupta", enrollmentId: "BCA2024006" },
    { name: "Kabir Rao", enrollmentId: "BCA2024007" },
    { name: "Isha Verma", enrollmentId: "BCA2024008" },
    { name: "Tarun Joshi", enrollmentId: "BCA2024009" },
    { name: "Sanya Roy", enrollmentId: "BCA2024010" }
  ];

  const studentsListEl = document.getElementById('students-list');
  const dateInput = document.getElementById('attendance-date');
  const saveBtn = document.getElementById('save-attendance');
  const resetBtn = document.getElementById('reset-attendance');
  const displayDateEl = document.getElementById('display-date');
  const displayTimeEl = document.getElementById('display-time');
  const attendanceSummaryBody = document.querySelector('#attendance-summary tbody');

  // Initialize date input with today's date
  function formatDateToYYYYMMDD(date) {
    return date.toISOString().split('T')[0];
  }
  // Format date for display in readable format
  function formatDateDisplay(dateString) {
    const d = new Date(dateString);
    return d.toLocaleDateString(undefined, { year: 'numeric', month: 'long', day: 'numeric' });
  }
  // Format time display (12h with seconds)
  function formatTimeDisplay(timeString) {
    // timeString in HH:MM:SS format or locale time string
    return timeString;
  }

  // Set initial date and render time display
  let currentTimeString = new Date().toLocaleTimeString();
  dateInput.value = formatDateToYYYYMMDD(new Date());

  function updateDateTimeDisplay() {
    displayDateEl.textContent = formatDateDisplay(dateInput.value);
    displayTimeEl.textContent = currentTimeString;
  }

  // Render students attendance list for a specific date
  function renderStudents(date) {
    studentsListEl.innerHTML = '';
    const attendanceRecords = getAttendanceData(date);

    students.forEach((student, index) => {
      const record = attendanceRecords.find(r => r.enrollmentId === student.enrollmentId);
      const status = record ? record.status : 'absent';

      const studentItem = document.createElement('div');
      studentItem.className = 'student-item ' + status;
      studentItem.setAttribute('role', 'listitem');
      studentItem.setAttribute('aria-label', `${student.name}, enrollment ID ${student.enrollmentId}, currently marked as ${status}`);

      const infoEl = document.createElement('div');
      infoEl.className = 'student-info';
      const nameEl = document.createElement('div');
      nameEl.className = 'student-name';
      nameEl.textContent = student.name;
      const enrollmentEl = document.createElement('div');
      enrollmentEl.className = 'student-enrollment';
      enrollmentEl.textContent = `Enrollment ID: ${student.enrollmentId}`;
      infoEl.appendChild(nameEl);
      infoEl.appendChild(enrollmentEl);

      const toggleEl = document.createElement('div');
      toggleEl.className = 'attendance-toggle';

      // Present radio button
      const presentId = `present-${index}`;
      const presentLabel = document.createElement('label');
      presentLabel.setAttribute('for', presentId);
      presentLabel.textContent = 'Present';
      const presentInput = document.createElement('input');
      presentInput.type = 'radio';
      presentInput.name = `attendance-${index}`;
      presentInput.id = presentId;
      presentInput.value = 'present';
      presentInput.checked = status === 'present';

      // Absent radio button
      const absentId = `absent-${index}`;
      const absentLabel = document.createElement('label');
      absentLabel.setAttribute('for', absentId);
      absentLabel.textContent = 'Absent';
      const absentInput = document.createElement('input');
      absentInput.type = 'radio';
      absentInput.name = `attendance-${index}`;
      absentInput.id = absentId;
      absentInput.value = 'absent';
      absentInput.checked = status === 'absent';

      // Update UI on change
      presentInput.addEventListener('change', () => {
        if (presentInput.checked) {
          studentItem.classList.remove('absent');
          studentItem.classList.add('present');
          studentItem.setAttribute('aria-label', `${student.name}, enrollment ID ${student.enrollmentId}, currently marked as present`);
        }
      });
      absentInput.addEventListener('change', () => {
        if (absentInput.checked) {
          studentItem.classList.remove('present');
          studentItem.classList.add('absent');
          studentItem.setAttribute('aria-label', `${student.name}, enrollment ID ${student.enrollmentId}, currently marked as absent`);
        }
      });

      presentLabel.prepend(presentInput);
      absentLabel.prepend(absentInput);
      toggleEl.appendChild(presentLabel);
      toggleEl.appendChild(absentLabel);

      studentItem.appendChild(infoEl);
      studentItem.appendChild(toggleEl);
      studentsListEl.appendChild(studentItem);
    });
  }

  // Retrieve attendance data from localStorage by date
  // Data Structure: array of objects { name, enrollmentId, status, time }
  function getAttendanceData(date) {
    const attendanceStr = localStorage.getItem('attendance_' + date);
    try {
      return attendanceStr ? JSON.parse(attendanceStr) : [];
    } catch (e) {
      return [];
    }
  }

  // Render attendance summary in table form
  function renderAttendanceSummary(date) {
    attendanceSummaryBody.innerHTML = '';
    const attendanceRecords = getAttendanceData(date);
    if (attendanceRecords.length === 0) {
      const tr = document.createElement('tr');
      const td = document.createElement('td');
      td.colSpan = 5;
      td.textContent = 'No attendance records found for this date.';
      td.style.textAlign = 'center';
      tr.appendChild(td);
      attendanceSummaryBody.appendChild(tr);
      return;
    }
    attendanceRecords.forEach(record => {
      const tr = document.createElement('tr');
      tr.className = record.status;

      const nameTd = document.createElement('td');
      nameTd.textContent = record.name;
      const enrollmentTd = document.createElement('td');
      enrollmentTd.textContent = record.enrollmentId;
      const statusTd = document.createElement('td');
      statusTd.textContent = record.status.charAt(0).toUpperCase() + record.status.slice(1);
      const dateTd = document.createElement('td');
      dateTd.textContent = formatDateDisplay(date);
      const timeTd = document.createElement('td');
      timeTd.textContent = record.time;

      tr.appendChild(nameTd);
      tr.appendChild(enrollmentTd);
      tr.appendChild(statusTd);
      tr.appendChild(dateTd);
      tr.appendChild(timeTd);

      attendanceSummaryBody.appendChild(tr);
    });
  }

  // Save attendance data for selected date
  function saveAttendance() {
    const date = dateInput.value;
    if (!date) {
      alert('Please select a valid date.');
      return;
    }

    // capture current time at save
    currentTimeString = new Date().toLocaleTimeString();
    updateDateTimeDisplay();

    const attendanceRecords = [];

    students.forEach((student, index) => {
      const radios = document.getElementsByName(`attendance-${index}`);
      radios.forEach(radio => {
        if (radio.checked) {
          attendanceRecords.push({
            name: student.name,
            enrollmentId: student.enrollmentId,
            status: radio.value,
            time: currentTimeString
          });
        }
      });
    });

    localStorage.setItem('attendance_' + date, JSON.stringify(attendanceRecords));
    alert(`Attendance saved for ${formatDateDisplay(date)} at ${currentTimeString}`);
    renderAttendanceSummary(date);
  }

  // Reset attendance for selected date (clear localStorage entry)
  function resetAttendance() {
    const date = dateInput.value;
    if (!date) {
      alert('Please select a valid date.');
      return;
    }
    if (confirm(`Are you sure you want to reset attendance for ${formatDateDisplay(date)}? This action cannot be undone.`)) {
      localStorage.removeItem('attendance_' + date);
      renderStudents(date);
      renderAttendanceSummary(date);
    }
  }

  // On date change, re-render students attendance and summary, and reset currentTimeString
  dateInput.addEventListener('change', () => {
    currentTimeString = new Date().toLocaleTimeString();
    updateDateTimeDisplay();
    renderStudents(dateInput.value);
    renderAttendanceSummary(dateInput.value);
  });

  saveBtn.addEventListener('click', saveAttendance);
  resetBtn.addEventListener('click', resetAttendance);

  // Initial render setup
  updateDateTimeDisplay();
  renderStudents(dateInput.value);
  renderAttendanceSummary(dateInput.value);

</script>
</body>
</html>
