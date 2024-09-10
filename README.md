Global Help Group

Owner: Ronu Chakma

Project Description:


Global Help Group is an innovative Android app designed to empower users to participate in online earning programs, such as cryptocurrency mining, airdrop participation, and ad view bonuses. The app combines these earning opportunities with a seamless and intuitive interface, allowing users to sign up using their email or phone number and activate their account by adding funds.

Global Help Group also integrates a powerful referral system where users can invite others to join and, in return, earn rewards and unlock bonuses. Each user receives a signup bonus upon registration, which can be withdrawn after referring at least two other users. This encourages community growth while rewarding early adopters.

Features:

User Authentication: Secure signup/login using email or phone.

Unique User ID: Each user is assigned a unique ID upon registration.

Fund Activation: Accounts must be activated by adding funds to unlock earning features.

Signup Bonus: All new users receive a bonus, but it can only be withdrawn after referring two others.

Referral Program: A structured referral system that incentivizes user growth.

Earnings: Users can earn through crypto mining, participating in airdrops, and watching ads.



---

Mission:

The mission of Global Help Group is to democratize access to online earning opportunities, particularly in the rapidly growing fields of cryptocurrency and digital marketing. We aim to empower individuals, regardless of location, to generate income from crypto mining, airdrops, and ad view programs, all while building a strong, collaborative user community.

Vision:

We envision a future where financial independence is attainable for all through digital means. Global Help Group aspires to become a global leader in the online earning ecosystem, fostering a thriving community where members can collaborate, refer others, and grow their income sustainably.


---

Installation

Prerequisites:

Android Studio

Firebase project setup for authentication and Firestore database


Steps to Install:

1. Clone the repository:

git clone https://github.com/yourusername/Global-Help-Group.git


2. Open the project in Android Studio.


3. Set up Firebase Authentication and Firestore:

Go to Firebase Console and create a new project.

Add Firebase to your Android app by following this guide.

Enable Authentication for Email/Phone and set up Firestore database.



4. Update your google-services.json file (download from Firebase console) and place it in your app/ directory.


5. Sync your project with Gradle and run the app on an emulator or physical device.




---

Code Snippets

Firebase Authentication and User Registration

This code demonstrates user signup via email and the creation of a unique user profile in Firestore.

FirebaseAuth mAuth = FirebaseAuth.getInstance();
FirebaseFirestore db = FirebaseFirestore.getInstance();

// Function to register a new user with email
public void registerUser(String email, String password) {
    mAuth.createUserWithEmailAndPassword(email, password)
        .addOnCompleteListener(this, task -> {
            if (task.isSuccessful()) {
                FirebaseUser user = mAuth.getCurrentUser();
                createUserProfile(user);
            } else {
                Toast.makeText(MainActivity.this, "Authentication failed.",
                    Toast.LENGTH_SHORT).show();
            }
        });
}

// Function to create a user profile in Firestore with unique ID
private void createUserProfile(FirebaseUser user) {
    String userID = UUID.randomUUID().toString(); // Generate a unique ID

    Map<String, Object> userProfile = new HashMap<>();
    userProfile.put("email", user.getEmail());
    userProfile.put("userID", userID);
    userProfile.put("funds", 0);  // Initially 0 funds
    userProfile.put("referredUsers", 0);  // Initially no referred users
    userProfile.put("signupBonus", true);  // Signup bonus available
    userProfile.put("isActive", false);    // User is not active until funds are added

    db.collection("users").document(user.getUid())
        .set(userProfile)
        .addOnSuccessListener(aVoid -> {
            Log.d(TAG, "User profile created successfully");
        })
        .addOnFailureListener(e -> {
            Log.w(TAG, "Error creating user profile", e);
        });
}

Add Funds to Activate User ID

This code allows users to add funds and activate their account.

public void addFunds(String userId, int amount) {
    DocumentReference userRef = db.collection("users").document(userId);

    userRef.update("funds", FieldValue.increment(amount))
        .addOnSuccessListener(aVoid -> {
            Log.d(TAG, "Funds added successfully");

            // Activate user if funds are added
            userRef.update("isActive", true);
        })
        .addOnFailureListener(e -> {
            Log.w(TAG, "Error adding funds", e);
        });
}

Referral System and Signup Bonus Withdrawal

This snippet demonstrates how the referral system works and how users can withdraw their signup bonus after referring two members.

// Function to update referral count
public void referUser(String referrerId, String referredEmail) {
    DocumentReference referrerRef = db.collection("users").document(referrerId);

    referrerRef.update("referredUsers", FieldValue.increment(1))
        .addOnSuccessListener(aVoid -> {
            Log.d(TAG, "Referral updated");

            // Check if the referrer is eligible for signup bonus withdrawal
            checkReferralEligibility(referrerId);
        })
        .addOnFailureListener(e -> {
            Log.w(TAG, "Error updating referral", e);
        });
}

// Function to check if the user can withdraw signup bonus
private void checkReferralEligibility(String userId) {
    DocumentReference userRef = db.collection("users").document(userId);

    userRef.get().addOnCompleteListener(task -> {
        if (task.isSuccessful()) {
            DocumentSnapshot document = task.getResult();
            if (document.exists()) {
                long referredUsers = document.getLong("referredUsers");

                // Check if user referred at least 2 members
                if (referredUsers >= 2) {
                    // Allow signup bonus withdrawal
                    withdrawSignupBonus(userId);
                }
            }
        } else {
            Log.d(TAG, "Error fetching user document", task.getException());
        }
    });
}

// Function to withdraw signup bonus
private void withdrawSignupBonus(String userId) {
    DocumentReference userRef = db.collection("users").document(userId);

    userRef.update("signupBonus", false)  // Bonus withdrawn
        .addOnSuccessListener(aVoid -> {
            Log.d(TAG, "Signup bonus withdrawn");
        })
        .addOnFailureListener(e -> {
            Log.w(TAG, "Error withdrawing signup bonus", e);
        });
}


---

Contributing

1. Fork the project


2. Create your feature branch (git checkout -b feature/newFeature)


3. Commit your changes (git commit -m 'Add some feature')


4. Push to the branch (git push origin feature/newFeature)


5. Open a pull request




---

License

This project is licensed under the MIT License - see the LICENSE file for details.

