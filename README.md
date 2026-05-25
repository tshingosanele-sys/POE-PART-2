package com.mycompany.loginsystem;

import java.util.*;
import java.text.SimpleDateFormat;
import java.io.FileWriter;
import java.io.IOException;
import java.util.regex.Pattern;

public class LoginSystem {

// ---------------- STORAGE ----------------
static ArrayList<String> ids = new ArrayList<>();
static ArrayList<String> messages = new ArrayList<>();
static ArrayList<String> recipients = new ArrayList<>();
static ArrayList<String> timestamps = new ArrayList<>();
static ArrayList<String> hashes = new ArrayList<>();
static ArrayList<String> statusList = new ArrayList<>();

static int sentCount = 0;
static int messageLimit = 0;

static Scanner input = new Scanner(System.in);

// ---------------- USERNAME VALIDATION ----------------
public static boolean checkUserName(String u) {

return u.contains("_") && u.length() <= 5;
}

// ---------------- PASSWORD VALIDATION ----------------
public static boolean checkPassword(String p) {

return Pattern.matches(
"^(?=.*[A-Z])(?=.*\\d)(?=.*[@#$%^&+=!]).{8,}$",
p
);
}

// ---------------- LOGIN ----------------
public static boolean login(
String u,
String p,
String su,
String sp
) {

return u.equals(su) && p.equals(sp);
}

// ---------------- PHONE VALIDATION ----------------
public static String validateNumber(String num) {

if (num == null) {
return "Invalid";
}

if (!num.startsWith("+27")) {
return "Invalid: must start with +27";
}

if (num.length() != 12) {
return "Invalid: must be 12 digits (+27XXXXXXXXX)";
}

if (!num.substring(3).matches("\\d{9}")) {
return "Invalid: only digits allowed after +27";
}

return "Valid";
}

// ---------------- HASH ----------------
public static String createMessageHash(
String id,
int num,
String msg
) {

String[] words = msg.trim().split("\\s+");

String first =
words.length > 0 ? words[0] : "MSG";

String last =
words.length > 1
? words[words.length - 1]
: words[0];

return (
id.substring(0, 2)
+ ":"
+ num
+ ":"
+ first
+ last
).toUpperCase();
}

// ---------------- SAVE FILE ----------------
public static void storeMessage() {

try {

FileWriter file =
new FileWriter("messages.json");

file.write("[\n");

for (int i = 0; i < messages.size(); i++) {

file.write(" {\n");

file.write(
" \"id\": \""
+ ids.get(i)
+ "\",\n"
);

file.write(
" \"hash\": \""
+ hashes.get(i)
+ "\",\n"
);

file.write(
" \"recipient\": \""
+ recipients.get(i)
+ "\",\n"
);

file.write(
" \"message\": \""
+ messages.get(i)
+ "\",\n"
);

file.write(
" \"status\": \""
+ statusList.get(i)
+ "\",\n"
);

file.write(
" \"time\": \""
+ timestamps.get(i)
+ "\"\n"
);

file.write(" }");

if (i < messages.size() - 1) {
file.write(",");
}

file.write("\n");
}

file.write("]");

file.close();

System.out.println(
"\n[SYSTEM] File saved successfully!"
);

System.out.println(
"[SYSTEM] Total records saved: "
+ messages.size()
);

} catch (IOException e) {

System.out.println(
"[ERROR] Failed to save file!"
);
}
}

// ---------------- SHOW MESSAGES ----------------
public static void showMessages() {

System.out.println("\n==============================");
System.out.println(" SHOW MESSAGES");
System.out.println("==============================");

System.out.println(
"[COMING SOON] This feature is under development."
);

System.out.println(
"Please check back later."
);

System.out.println("==============================\n");
}

// ---------------- MAIN ----------------
public static void main(String[] args) {

System.out.println(
"=== QUICKCHAT CONSOLE ==="
);

// ---------------- REGISTRATION ----------------
String su;
String sp;

// USERNAME LOOP
while (true) {

System.out.print(
"Create Username: "
);

su = input.nextLine();

if (checkUserName(su)) {

System.out.println(
"[SUCCESS] Username accepted."
);

break;
}

System.out.println(
"[ERROR] Username must contain '_' and be 5 characters or less."
);
}

// PASSWORD LOOP
while (true) {

System.out.print(
"Create Password: "
);

sp = input.nextLine();

if (checkPassword(sp)) {

System.out.println(
"[SUCCESS] Password accepted."
);

break;
}

System.out.println(
"[ERROR] Password must contain:"
);

System.out.println("- 8+ characters");
System.out.println("- 1 capital letter");
System.out.println("- 1 number");
System.out.println("- 1 special character");
}

// ---------------- LOGIN ----------------
boolean logged = false;

while (!logged) {

System.out.print(
"Login Username: "
);

String u = input.nextLine();

System.out.print(
"Login Password: "
);

String p = input.nextLine();

logged = login(u, p, su, sp);

if (!logged) {

System.out.println(
"[ERROR] Incorrect username or password."
);

System.out.println(
"Please try again.\n"
);

} else {

System.out.println(
"[SUCCESS] Login successful!"
);
}
}

System.out.println(
"\nWelcome to QuickChat"
);

// ---------------- MESSAGE LIMIT LOOP ----------------
while (true) {

try {

System.out.print(
"How many messages do you want to send? "
);

messageLimit =
Integer.parseInt(
input.nextLine()
);

if (messageLimit <= 0) {

System.out.println(
"[ERROR] Enter a number greater than 0."
);

continue;
}

break;

} catch (NumberFormatException e) {

System.out.println(
"[ERROR] Numbers only."
);
}
}

// ---------------- MENU ----------------
while (true) {

System.out.println(
"\n========== MENU =========="
);

System.out.println(
"1. Send Message"
);

System.out.println(
"2. Show Messages"
);

System.out.println(
"3. Discard Last Message"
);

System.out.println(
"4. Save Messages"
);

System.out.println(
"5. Quit"
);

System.out.println(
"=========================="
);

System.out.print(
"Choose option: "
);

String choice =
input.nextLine();

switch (choice) {

case "1":

if (sentCount >= messageLimit) {

System.out.println(
"[WARNING] Message limit reached!"
);

break;
}

String rec;

// RECIPIENT LOOP
while (true) {

System.out.print(
"Enter Recipient (+27XXXXXXXXX): "
);

rec = input.nextLine();

String validation =
validateNumber(rec);

if (validation.equals("Valid")) {
break;
}

System.out.println(
"[ERROR] " + validation
);
}

String msg;

// MESSAGE LOOP
while (true) {

System.out.print(
"Enter Message (max 250 chars): "
);

msg = input.nextLine();

if (msg.length() <= 250) {
break;
}

System.out.println(
"[ERROR] Message too long!"
);
}

String id =
String.format(
"%010d",
(long)
(Math.random()
* 10000000000L)
);

String hash =
createMessageHash(
id,
sentCount,
msg
);

ids.add(id);
hashes.add(hash);
recipients.add(rec);
messages.add(msg);

timestamps.add(
new SimpleDateFormat(
"HH:mm:ss"
).format(new Date())
);

statusList.add("SENT");

sentCount++;

System.out.println(
"\n[SUCCESS] MESSAGE SENT"
);

System.out.println(
"ID : " + id
);

System.out.println(
"HASH : " + hash
);

break;

case "2":

showMessages();
break;

case "3":

if (messages.isEmpty()) {

System.out.println(
"[INFO] No messages to discard."
);

break;
}

int last =
messages.size() - 1;

System.out.println(
"[SYSTEM] Discarding last message..."
);

System.out.println(
"Removed: "
+ messages.get(last)
);

ids.remove(last);
hashes.remove(last);
recipients.remove(last);
messages.remove(last);
timestamps.remove(last);
statusList.remove(last);

sentCount--;

System.out.println(
"[SUCCESS] Message discarded."
);

break;

case "4":

storeMessage();
break;

case "5":

System.out.print(
"Save before exit? (yes/no): "
);

String save =
input.nextLine();

if (save.equalsIgnoreCase("yes")) {

storeMessage();
}

System.out.println(
"Total messages sent: "
+ sentCount
);

System.out.println(
"Goodbye!"
);

return;

default:

System.out.println(
"[ERROR] Invalid option! Try again."
);
}
}
}
}
