<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Get Your Blood</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="circle">
        <span class="circle-text">About Us</span>
        <span class="circle-text">Register</span>
        <span class="circle-text">Log In</span>
    </div>

    <div id="landing-page">
        <h1>Get Your Blood</h1>
        <div id="intro">
            <p>Welcome to the Get Your Blood platform where you can either donate blood or request blood from willing donors. Your contribution helps save lives!</p>
        </div>
        <button id="locationButton">Get Location</button>
        <div id="location"></div>
        <div class="info">
            <p>Our mission is to connect blood donors and recipients, ensuring a safe and fast way to provide and receive blood when needed. You can also earn a certification for donating blood.</p>
        </div>
    </div>

    <script src="script.js"></script>
</body>
</html>
/* Basic Styles */
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f9;
    color: #333;
    margin: 0;
    padding: 0;
}

/* Circle Menu */
#circle {
    width: 200px;
    height: 200px;
    background-color: #3498db;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    animation: rotate 5s infinite linear;
}

.circle-text {
    color: white;
    font-size: 18px;
    position: absolute;
    text-align: center;
}

.circle-text:nth-child(1) { top: 10px; left: 10px; }
.circle-text:nth-child(2) { top: 80px; left: 80px; }
.circle-text:nth-child(3) { bottom: 10px; right: 10px; }

/* Landing Page */
#landing-page {
    text-align: center;
    padding: 30px;
}

h1 {
    font-size: 36px;
    margin-bottom: 20px;
}

/* Animations */
@keyframes rotate {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

/* Location button */
#locationButton {
    background-color: #e74c3c;
    padding: 10px 20px;
    border: none;
    color: white;
    cursor: pointer;
    border-radius: 5px;
    margin-top: 20px;
}

#location {
    margin-top: 10px;
    font-size: 18px;
}

/* Info Section */
.info {
    background-color: #2ecc71;
    padding: 15px;
    color: white;
    margin-top: 20px;
    border-radius: 5px;
}
// Get Location button functionality
document.getElementById('locationButton').addEventListener('click', () => {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition, showError);
    } else {
        document.getElementById('location').innerHTML = "Geolocation is not supported by this browser.";
    }
});

function showPosition(position) {
    const location = `Latitude: ${position.coords.latitude}, Longitude: ${position.coords.longitude}`;
    document.getElementById('location').innerHTML = location;
}

function showError(error) {
    switch(error.code) {
        case error.PERMISSION_DENIED:
            document.getElementById('location').innerHTML = "User denied the request for Geolocation.";
            break;
        case error.POSITION_UNAVAILABLE:
            document.getElementById('location').innerHTML = "Location information is unavailable.";
            break;
        case error.TIMEOUT:
            document.getElementById('location').innerHTML = "The request to get user location timed out.";
            break;
        case error.UNKNOWN_ERROR:
            document.getElementById('location').innerHTML = "An unknown error occurred.";
            break;
    }
}
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
@Entity
public class Donor {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String bloodGroup;
    private String mobileNumber;
    private String healthCondition;
    private boolean alcoholConsumption;
    private boolean isDonor;
    
    // Getters and setters
}
@RestController
@RequestMapping("/donor")
public class DonorController {

    @Autowired
    private DonorService donorService;

    @PostMapping("/register")
    public ResponseEntity<Donor> registerDonor(@RequestBody Donor donor) {
        Donor registeredDonor = donorService.registerDonor(donor);
        return ResponseEntity.ok(registeredDonor);
    }

    @PostMapping("/login")
    public ResponseEntity<String> login(@RequestBody LoginDetails loginDetails) {
        boolean isAuthenticated = donorService.authenticate(loginDetails);
        if (isAuthenticated) {
            return ResponseEntity.ok("Login successful");
        } else {
            return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid credentials");
        }
    }
}
@Service
public class DonorService {

    @Autowired
    private DonorRepository donorRepository;

    public Donor registerDonor(Donor donor) {
        return donorRepository.save(donor);
    }

    public boolean authenticate(LoginDetails loginDetails) {
        Optional<Donor> donor = donorRepository.findByMobileNumber(loginDetails.getMobileNumber());
        return donor.isPresent() && donor.get().getPassword().equals(loginDetails.getPassword());
    }
}
public class LoginDetails {
    private String mobileNumber;
    private String password;
    
    // Getters and setters
}
