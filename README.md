Download Link: https://assignmentchef.com/product/solved-solvedprogram-4
<br>
Program DescriptionYou will develop a multi-threaded Web server that is capable of processing multiple service requests simultaneously. You should demonstrate that your Web server is capable of delivering a web page to a Web browser. You must implement HTTP version 1.0, where separate HTTP requests are sent for each component of the Web page. Your Web Server will be “listening” on port 5976 for the connection requests from Clients.When it receives a TCP connection request, it sets up a TCP connection through another port and services the request in a separate thread.As you are developing the code, you can test your server from a Web browser. But remember that you are NOT serving through the standard port 80, so you need to specify the port number within the URL that you give to your browser. For example, if your machine’s name is host.someschool.edu, your server is listening to port 5976, and you want to retrieve the file index.html, then you would specify the following URL within the browser: http://host.someschool.edu:5976/index.html. If you omit “:5976”, the browser will assume port 80 which most likely will not have a server listening on it. When the server encounters an error, it sends a response message with the appropriate HTML source so that the error information is displayed in the browserwindow.Program Requirement1. Your Web Server must not crash under any situation.2. You must try-catch everything and print out appropriate error messages identifying the specific exception.3. You MUST follow the software development ground rules from CS2430 (posted on D2L.)4. You have the option to work on this program with a partner from any sections. The deadline for signing up pair programming is 10/23. One of the group members is responsible for submitting the project folder to S:CoursesCSSEchanglcs38301dropBox by the due date.5. You MUST DEMO your program by the due date. If you work as a pair, both of you MUST show up for demoing. Demo schedule will be announced on D2L.6. You MUST have the following classes and follow the program structure and instructions below.&#x1; WebServer classThe Web server will be multi-threaded, where the processing of each incoming request will take place inside a separate thread of execution (HttpRequest). The program structure of the Server is shown below:import java.io.* ;import java.net.* ;import java.util.* ;public class WebServer{public static void main(String argv[]) throws Exception{. . .}Public void run(){. . .}}class HttpRequest extends Thread{. . .}

In the run() method of WebServer class, when a connection request is received, create an instance of HttpRequest with the Socket object that is created to handle the connection.HttpRequest request = new HttpRequest( ? ); //new instance of a HTTP requestThread thread = new Thread(request); //create a new thread to handle the request thread.start(); //start the thread&#x1; HttpRequest classAccording to the HTTP specification, you need to terminate each line of the server’s response message with a carriage return (CR) and a line feed (LF), so you can define a constant CRLF as a convenience. The variablesocket will be used to store a reference to the connection socket, which is passed to the constructor of HttpRequest. Most of the processing will take place within processRequest(), which is called from within run(). The structure of the HttpRequest class is shown below:class HttpRequest extends Thread{private final static String CRLF = “r
”;private Socket socket;. . .public HttpRequest(Socket client) // Constructor{socket = client;}// Override the run() method of the Thread classpublic void run(){try{processRequest();}?}private void processRequest() throws Exception{// Get a reference to the socket’s input and output streams.InputStream socketIn = ?;DataOutputStream socketOut = ?;// Set up input stream filters.BufferedReader br = ?;. . .// Get the request line of the HTTP request message.String requestLine = ?;. . .// Extract the filename from the request line.StringTokenizer tokens = new StringTokenizer(requestLine);. . .// Open the requested file.. . .// Construct the response message.// Send the status line.// Send the content type line.// Send a blank line to indicate the end of the header lines.// Send the entity body.// Close streams and socket.}}Note that we cannot throw exceptions from run(), because we must strictly adhere to the declaration of run() in the Thread class, which does not throw any exceptions. We will place all the processing code in processRequest(),and from there, throw exceptions to run(). Within run(), we explicitly catch and handle exceptions with a try/catch block.

&#x1; processRequest() method• Read the request message from the socket’s input stream. The readLine() method of the BufferedReader class will extract characters from the input stream until it reaches an end-of-line character, or in our case, the end-of-line character sequence CRLF. The first item available in the input stream will be the HTTP request line. (See Section 2.2 of the textbook for a description of this and thefollowing fields.)• Extract the file name from the request line with the StringTokenizer class. Skip over the method specification, which we have assumed to be “GET”, and extract the file name only. Because the browser precedes the filename with a slash, prefix a dot so that the resulting pathname starts within the current directory. For example, fileName = “.” + fileName;• Open the file as the first step in sending it to the client. If the file does not exist, the FileInputStream() constructor will throw the FileNotFoundException. Instead of throwing this possible exception and terminating the thread, we will use a try/catch construction to set the boolean variable fileExists to false.Later in the code, we will use this flag to construct an error response message, rather than try to send a nonexistent file.• There are three parts to the response message: the status line, the response headers, and the entity body.The status line and response headers are terminated by the character sequence CRLF. We are going to respond with a status line, which we store in the variable statusLine, and a single response header,which we store in the variable contentTypeLine. In the case of a request for a nonexistent file, we return 404 Not Found in the status line of the response message, and include an error message in the form of an HTML document in the entity body.// Construct the response message.String statusLine = null;String contentTypeLine = null;String entityBody = null;if (fileExists){statusLine = ?;contentTypeLine = “Content-type: ” + contentType( fileName ) + CRLF;}else{statusLine = ?;contentTypeLine = ?;entityBody = “&lt;HTML” + “&lt;HEAD + ” }When the file exists, we need to determine the file’s MIME type and send the appropriate MIME-type specifier. We make this determination in a separate private method called contentType(), which returns a string that we can include in the content type line that we are constructing.• Send the status line and our single header line to the browser by writing into the socket’s output stream.• Send the entity body if the requested file exists. Call a separate method to send the file. If the requested file does not exist, we send the HTML-encoded error message that we have prepared.// Send the entity body.if (fileExists){sendBytes(fis, os);fis.close();}elseos.writeBytes(?);

• contentType(), the method that determines the MIME type. This method will examine the extension of a file name and return a string that represents its MIME type. If the file extension is unknown, we return the type application/octet-stream.private static String contentType(String fileName){if(fileName.endsWith(“.htm”) || fileName.endsWith(“.html”)){?;}if(?){?;}if(?){?;}return “application/octet-stream”;}There is a lot missing from this method. For instance, nothing is returned for GIF or JPEG files. You may want to add the missing file types yourself, so that the components of your home page are sent with the content type correctly specified in the content type header line. For GIFs the MIME type is image/gif andfor JPEGs it is image/jpeg.• SendBytes(), the method that writes the requested file onto the socket’s output stream. This method may throw exceptions. Instead of catching these exceptions and handling them in our code, we throw them to be handled by the calling method.private static void sendBytes(FileInputStream fis, OutputStream os) throws Exception{// Construct a 1024 bytes buffer to hold bytes on their way to the socket.byte[] buffer = new byte[BUFFER_SIZE];int bytes = 0;// Copy requested file into the socket’s output stream.While(?){os.write(buffer, 0, bytes);}}