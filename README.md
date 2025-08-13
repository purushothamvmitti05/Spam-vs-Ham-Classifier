Live Demo: 
https://purushothamvmitti05.github.io/Spam-vs-Ham-Classifier/

Table of Contents

Understanding the Problem
Project Setup
Building the User Interface
Creating the Classification Algorithm
Adding Interactive Features
Testing and Validation
Deployment and Usage
Next Steps and Improvements


1. Understanding the Problem
What is Spam Classification?

Spam: Unwanted, unsolicited messages (usually promotional or fraudulent)
Ham: Legitimate, wanted messages from real people or organizations
Goal: Automatically identify which category a message belongs to

Common Spam Characteristics

Urgent language ("ACT NOW!", "LIMITED TIME!")
Money-related terms ("FREE", "$$$", "CASH")
Excessive punctuation ("!!!", "???")
All capital letters
Phone numbers and suspicious links
Poor grammar and spelling

Legitimate Message Characteristics

Personal tone and proper grammar
Specific details (appointments, meetings)
Professional language
Contextual information
No excessive promotional language


2. Project Setup
Step 2.1: Create Project Structure
spam-classifier/
├── index.html          # Main HTML file
├── styles.css          # CSS styling (optional - we'll use inline)
├── script.js           # JavaScript logic (optional - we'll use inline)
└── README.md           # Documentation
Step 2.2: Choose Development Approach
For beginners, we'll use a single HTML file approach:

All HTML, CSS, and JavaScript in one file
No external dependencies
Easy to understand and modify
Can run directly in any web browser

Step 2.3: Set Up Basic HTML Structure
html<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spam vs Ham Classifier</title>
</head>
<body>
    <!-- Content will go here -->
</body>
</html>

3. Building the User Interface
Step 3.1: Design the Layout

Header Section

App title and description
Eye-catching design with gradients


Input Section

Large textarea for message input
Clear labeling and instructions


Action Buttons

"Analyze Message" button (primary action)
"Clear All" button (secondary action)


Results Section

Classification result (Spam/Ham)
Confidence percentage
Visual indicators (colors, icons)


Features Section

Show detected keywords and patterns
Educational purpose


Examples Section

Pre-written spam and ham examples
Click to load into input



Step 3.2: Create HTML Structure
html<div class="container">
    <div class="header">
        <h1>🛡️ Spam vs Ham Classifier</h1>
        <p>Intelligent SMS and Email spam detection</p>
    </div>
    
    <div class="main-content">
        <!-- Input section -->
        <div class="input-section">
            <label for="messageInput">Enter your message:</label>
            <textarea id="messageInput" placeholder="Type message here..."></textarea>
        </div>
        
        <!-- Buttons -->
        <div class="button-group">
            <button onclick="classifyMessage()">Analyze Message</button>
            <button onclick="clearAll()">Clear All</button>
        </div>
        
        <!-- Results -->
        <div id="resultSection">
            <!-- Results will appear here -->
        </div>
    </div>
</div>
Step 3.3: Add CSS Styling
css/* Modern, responsive design */
body {
    font-family: 'Segoe UI', sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    margin: 0;
    padding: 20px;
}

.container {
    max-width: 900px;
    margin: 0 auto;
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.1);
}

/* Add more styles for buttons, inputs, etc. */

4. Creating the Classification Algorithm
Step 4.1: Define Spam Keywords
javascriptconst spamKeywords = [
    // Urgency words
    'urgent', 'limited time', 'act now', 'expires today',
    
    // Money words
    'free', 'cash', 'money', 'loan', 'winner', 'prize',
    
    // Action words
    'click here', 'call now', 'text stop', 'don\'t miss',
    
    // Promotional words
    'amazing deal', 'special offer', 'exclusive', 'bonus'
];
Step 4.2: Create Pattern Detection
javascriptconst spamPatterns = [
    /\$\d+/g,                    // Dollar amounts ($100, $50)
    /\b\d{3}-\d{3}-\d{4}\b/g,    // Phone numbers (555-123-4567)
    /click here/gi,              // "Click here" phrases
    /FREE|Win/gi,                // Common spam words
    /URGENT|ASAP/gi,             // Urgency indicators
    /[!]{2,}/g                   // Multiple exclamation marks
];
Step 4.3: Build the Scoring System
javascriptfunction analyzeMessage(message) {
    const lowerMessage = message.toLowerCase();
    let spamScore = 0;
    const detectedFeatures = [];

    // 1. Check for spam keywords
    spamKeywords.forEach(keyword => {
        if (lowerMessage.includes(keyword.toLowerCase())) {
            spamScore += 10;  // Add 10 points for each spam keyword
            detectedFeatures.push({
                text: `Spam keyword: "${keyword}"`,
                type: 'spam'
            });
        }
    });

    // 2. Check for suspicious patterns
    spamPatterns.forEach(pattern => {
        const matches = message.match(pattern);
        if (matches) {
            spamScore += 15;  // Add 15 points for each pattern
            matches.forEach(match => {
                detectedFeatures.push({
                    text: `Suspicious pattern: "${match}"`,
                    type: 'spam'
                });
            });
        }
    });

    // 3. Check for legitimate indicators
    const hamKeywords = [
        'meeting', 'appointment', 'schedule', 'reminder',
        'thank you', 'please', 'regards', 'confirmed'
    ];
    
    hamKeywords.forEach(keyword => {
        if (lowerMessage.includes(keyword)) {
            spamScore -= 8;  // Subtract points for legitimate words
            detectedFeatures.push({
                text: `Legitimate word: "${keyword}"`,
                type: 'ham'
            });
        }
    });

    return {
        score: spamScore,
        features: detectedFeatures
    };
}
Step 4.4: Make Classification Decision
javascriptfunction classifyMessage() {
    const message = document.getElementById('messageInput').value.trim();
    
    if (!message) {
        showResult('neutral', 'Please enter a message');
        return;
    }

    const analysis = analyzeMessage(message);
    const confidence = Math.min(95, Math.max(55, Math.abs(analysis.score) + 50));

    if (analysis.score > 20) {
        // High spam score = likely spam
        showResult('spam', '🚫 This message appears to be SPAM', confidence);
    } else if (analysis.score < -10) {
        // Negative score = likely legitimate
        showResult('ham', '✅ This message appears to be LEGITIMATE', confidence);
    } else {
        // Uncertain classification
        const type = analysis.score > 0 ? 'spam' : 'ham';
        const message = type === 'spam' ? 
            '⚠️ This message might be SPAM' : 
            '✅ This message appears to be LEGITIMATE';
        showResult(type, message, confidence - 20);
    }
    
    showFeatures(analysis.features);
}

5. Adding Interactive Features
Step 5.1: Create Result Display Function
javascriptfunction showResult(type, message, confidence) {
    const resultSection = document.getElementById('resultSection');
    
    // Update styling based on result
    resultSection.className = `result-section result-${type}`;
    
    // Update content
    resultSection.innerHTML = `
        <div class="result-title">${message}</div>
        <div class="result-confidence">Confidence: ${Math.round(confidence)}%</div>
    `;
}
Step 5.2: Add Example Messages
javascriptconst examples = [
    {
        type: 'ham',
        text: 'Hey, are we still meeting for lunch tomorrow at 12:30?'
    },
    {
        type: 'spam',
        text: 'URGENT! You\'ve won $5000! Click here NOW to claim your prize!'
    },
    {
        type: 'ham',
        text: 'Your appointment with Dr. Smith is confirmed for Friday at 3 PM.'
    },
    {
        type: 'spam',
        text: 'FREE MONEY! No credit check needed! Apply now for instant cash!'
    }
];

function useExample(element) {
    const messageText = element.querySelector('.message-text').textContent;
    document.getElementById('messageInput').value = messageText;
}
Step 5.3: Add Feature Detection Display
javascriptfunction showFeatures(features) {
    const featureSection = document.getElementById('featureSection');
    const featureList = document.getElementById('featureList');
    
    if (features && features.length > 0) {
        featureSection.style.display = 'block';
        featureList.innerHTML = features.map(feature => 
            `<span class="feature-tag feature-${feature.type}">
                ${feature.text}
            </span>`
        ).join('');
    } else {
        featureSection.style.display = 'none';
    }
}

6. Testing and Validation
Step 6.1: Test Cases to Try
Spam Examples:

"FREE iPhone! Limited time offer! Click now!"
"URGENT: Your account will be closed! Call 555-0123"
"Congratulations! You've won $1000 cash prize!"
"Amazing discount! 90% off! Don't miss out!"

Ham Examples:

"Meeting scheduled for tomorrow at 2 PM in conference room B"
"Thank you for your email. I'll respond by Friday."
"Reminder: Doctor's appointment on Thursday at 10 AM"
"Can you please send me the project report when ready?"

Step 6.2: Validation Checklist

 Does it correctly identify obvious spam?
 Does it correctly identify legitimate messages?
 Are the confidence scores reasonable?
 Do the detected features make sense?
 Is the interface responsive on mobile?
 Do all buttons work correctly?

Step 6.3: Fine-tuning the Algorithm
javascript// Adjust scoring weights if needed
const KEYWORD_WEIGHT = 10;    // Points per spam keyword
const PATTERN_WEIGHT = 15;    // Points per suspicious pattern
const HAM_WEIGHT = -8;        // Points deducted for legitimate words
const SPAM_THRESHOLD = 20;    // Score above which = spam
const HAM_THRESHOLD = -10;    // Score below which = ham

7. Deployment and Usage
Step 7.1: Local Testing

Save the complete HTML file as spam-classifier.html
Open it in any modern web browser
Test with various message types
Verify all features work correctly

Step 7.2: Sharing Your Application
Option 1: File Sharing

Share the HTML file directly
Recipients can open it in their browser
No internet connection required

Option 2: GitHub Pages

Create a GitHub repository
Upload your HTML file as index.html
Enable GitHub Pages in repository settings
Share the generated URL

Option 3: Web Hosting

Upload to any web hosting service
Works on shared hosting, Netlify, Vercel, etc.

Step 7.3: Usage Instructions

Open the application in your web browser
Enter a message in the text area
Click "Analyze Message" to get results
Review the classification and confidence score
Check detected features to understand the decision
Try examples to learn about different message types
Use "Clear All" to start over


8. Next Steps and Improvements
Step 8.1: Basic Improvements

 Add more spam keywords and patterns
 Improve the scoring algorithm
 Add support for different languages
 Include email-specific patterns (headers, attachments)
 Add a "Report Error" feature

Step 8.2: Advanced Features

 Machine Learning Integration

Use TensorFlow.js for more sophisticated classification
Train on real spam/ham datasets


 Real-time Analysis

Analyze as user types
Show live confidence scores


 Batch Processing

Upload and analyze multiple messages
Export results to CSV


 API Integration

Connect to external spam detection services
Use cloud-based ML models



Step 8.3: Learning Opportunities

Study Real Datasets

SMS Spam Collection Dataset
Enron Email Dataset
SpamAssassin Public Corpus


Learn Machine Learning

Naive Bayes classifier
Support Vector Machines (SVM)
Neural networks


Explore NLP Techniques

Tokenization and stemming
TF-IDF (Term Frequency-Inverse Document Frequency)
Word embeddings


Security Considerations

Prevent adversarial attacks
Handle obfuscated text
Privacy protection



Step 8.4: Integration Ideas

Email Clients: Browser extension for Gmail, Outlook
Mobile Apps: React Native or Flutter version
Chatbots: Integrate with Telegram, Discord bots
Business Tools: Add to customer service platforms


🎯 Key Takeaways
What You've Learned

Problem Analysis: Understanding spam vs legitimate messages
Algorithm Design: Creating rule-based classification
Web Development: Building responsive user interfaces
Feature Engineering: Identifying important text patterns
User Experience: Making complex algorithms user-friendly

Skills Developed

HTML/CSS for modern web interfaces
JavaScript for interactive functionality
Pattern matching and text analysis
Algorithm design and optimization
User interface/user experience design

Real-World Applications

Email security systems
Social media content moderation
Customer service automation
Mobile app message filtering
Business communication tools


📚 Additional Resources
Documentation

MDN Web Docs - HTML, CSS, JavaScript
Regular Expressions - Pattern matching
Machine Learning Basics

Datasets

SMS Spam Collection
Enron Email Dataset
SpamAssassin Corpus

Tools

TensorFlow.js - Machine learning in browsers
Natural - Natural language processing
Chart.js - Data visualization

This guide provides a complete foundation for understanding and building spam classifiers. Start with the basic version and gradually add more sophisticated features as you learn!
