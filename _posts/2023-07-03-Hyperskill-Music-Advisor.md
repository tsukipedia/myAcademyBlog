---
category: Logs
title: Hyperskill - Music Advisor
date: 2023-07-03
---

# OVERVIEW
The project involves developing a command line Java application that interacts with the Spotify API to offer music-related functionalities. The application allows users to register using third-party services and provides information based on user requests. The project progresses through several stages, starting with implementing basic functionality to read user input and provide information. Subsequent stages focus on adding authentication using OAuth, incorporating a simple HTTP server, making API requests, and implementing paginated output. The application follows the Model-View-Controller (MVC) pattern and allows users customize API URLs and the number of entries per page.

# CONTEXT
The problem this project addresses is the need for a streamlined and interactive application for users to explore music content from Spotify without having to navigate through Spotify's application or website.

The project is implemented in stages:

- Stage 1 lays the foundation where the application can accept user commands and simulate responses as if they were fetched from Spotify's API.

- Stage 2 introduces OAuth authorization. It simulates the process of generating an authorization link, which would eventually be used to authorize the application to access Spotify on behalf of the user.

- Stage 3 is where the application integrates a real authorization flow by creating an HTTP server to capture the OAuth authorization code from Spotify. It also enables the application to acquire an access token.

- Stage 4 replaces the simulated responses with real API calls to Spotify. The application now communicates with Spotify’s API to fetch the actual data in response to user commands.

- Stage 5 refactors the application following the MVC design pattern and introduces paginated output. The output is structured across pages, with options for the user to navigate between them.

This project is of importance as it showcases how to effectively integrate with a third-party service (Spotify) securely using OAuth, make REST API calls, parse responses, and structure an application according to MVC design pattern. The paginated output ensures usability and an organized presentation of data. 

## Available commands

### Build commands

```
> -access [auth API URL]
```

This command passes the OAuth API URL as a build argument.

```
> -resource [web API URL]
```
This command passes the web API URL as a build argument.

```
> -page [page size]
```

This command passes the maximum number of elements per page.

### Runtime commands
- `auth`: Prints the auth link and allow us to use another any other command.
```
> auth
https://accounts.spotify.com/authorize?client_id=1234&redirect_uri=http://localhost:8080&response_type=code
waiting for code...
code received
making http request for access_token...
{"access_token":"abcd,"token_type":"Bearer","expires_in":3600,"refresh_token":"efgh"}
---SUCCESS---
```

- `featured`: Prints a list of Spotify-featured playlists with their links fetched from API.

```
> featured 
---PLAYLISTS---
lofi beats
https://open.spotify.com/playlist/37i9dQZF1DWWQRwui0ExPn

Tarea Casual
https://open.spotify.com/playlist/37i9dQZF1DX0yN5997BIDH

Kaleidoscope
https://open.spotify.com/playlist/37i9dQZF1DX3PsJxMtn1AP

Jazz Tranquilo
https://open.spotify.com/playlist/37i9dQZF1DX05Q6G4VDGLq

Coffee and Piano
https://open.spotify.com/playlist/37i9dQZF1DX3TPMgP3ojGS

---PAGE 1 OF 2---
```

- `new`: Prints a list of new albums with artists and links on Spotify.

```
> new 
---NEW RELEASES---
Nata Montana
[Natanael Cano]
https://open.spotify.com/album/1YzV3eSAyofYe6QqIaZrj7

DATA
[Tainy]
https://open.spotify.com/album/6xRxlUUfg3M0QB1LUX89gA

Copa Vacía
[Shakira, Manuel Turizo]
https://open.spotify.com/album/4qtiO9UGqajxnKC0z0Mxn7

vampire
[Olivia Rodrigo]
https://open.spotify.com/album/5kqfR7EuGbyp8x27Pr1kY9

GÉNESIS
[Peso Pluma]
https://open.spotify.com/album/4jox3ip1I39DFC2B7R5qLH

---PAGE 1 OF 20---

```

- `categories`: Prints a list of all available categories on Spotify (just their names).

```
> categories
---CATEGORIES---
Top Lists
Regional Mexican
Music of Mexico
Summer
Pop
---PAGE 1 OF 12---
```

- `playlists [category name]`: Prints a list that contains playlists of a specific category and their links on Spotify.

```
> playlists Summer
---PLAYLISTS---
Summer Dance Hits 2023
https://open.spotify.com/playlist/37i9dQZF1DWZ7eJRBxKzdO

Ibiza 2023
https://open.spotify.com/playlist/37i9dQZF1DXaCACvgOVs5K

Happy Beats
https://open.spotify.com/playlist/37i9dQZF1DWSf2RDTDayIx

Summer House 2023
https://open.spotify.com/playlist/37i9dQZF1DX05r4Oy3Ln97

Tropical House
https://open.spotify.com/playlist/37i9dQZF1DX0AMssoUKCz7

---PAGE 1 OF 39---
```

- `next`: Go to next page

```
> categories
---CATEGORIES---
Top Lists
Regional Mexican
Music of Mexico
Summer
Pop
---PAGE 1 OF 12---
> next
---CATEGORIES---
Latin
Romance
Sleep
In the car
Netflix
---PAGE 2 OF 12---
```

- `prev`: Go to previous page

```
> categories
---CATEGORIES---
Top Lists
Regional Mexican
Music of Mexico
Summer
Pop
---PAGE 1 OF 12---
> next
---CATEGORIES---
Latin
Romance
Sleep
In the car
Netflix
---PAGE 2 OF 12---
> prev
---CATEGORIES---
Top Lists
Regional Mexican
Music of Mexico
Summer
Pop
---PAGE 1 OF 12---
```

- `exit` Shuts down the application.

```
> exit 
---GOODBYE!---
```



# SOLUTION
## Running the app
To begin execution, the app should be able to receive three CLI arguments and use them as the program's configurations.

``` java
public class Main {

    public static void main(String[] args) throws IOException, InterruptedException {
        for (int i = 0; i < args.length - 1; i = i + 2) setConfigs(args[i], args[i + 1]);

        // more code...
    }

    public static void setConfigs(String command, String input) {
        switch (command) {
            case "-access" -> Authentication.setBaseUrl(input);
            case "-resource" -> API.setBaseUrl(input);
            case "-page" -> API.setPageSize(input);
        }
    }

}
```

This reads the arguments in a for loop and assigns them to their respective variables in the switch statement inside `setConfigs()` method.

## Reading user commands
The main class also contains the logic to read the user input commands and exit execution when the user inputs ``> exit``

``` java
public class Main {

    public static void main(String[] args) throws IOException, InterruptedException {

        // more code...

        Scanner input = new Scanner(System.in);
        String currentInput = "";

        while (!(currentInput = input.nextLine()).equals("exit")) {
            try {
                InputReader.evaluateUserInput(currentInput);
            }
            catch (Exception e) {
                System.out.println(e.getMessage());
            }
        }
        System.out.println("---GOODBYE!---");
        input.close();
    }
}
```

To process the user input we have a method inside the class `InputReader` called `evaluateUserInput()`, this class is part of the Controller because it receives the user input and fetches the required data from the API.

``` java
public class InputReader {
    private static User user = new User();
    private static String accessToken = "";
    private static PaginatedContent apiCaller;
    private static Printer printer;

    public static void evaluateUserInput(String input) throws IOException, InterruptedException {

        if (!input.equals("auth") && !user.isAuthenticated()) {
            Printer.printAuthError();
            return;
        }

        List content;
        switch (input) {
            case "auth" -> {
                // authentication logic
            }
            case "featured" -> {
                // get featured playlists logic
            }
            case "new" -> {
                // get new releases logic
            }
            case "categories" -> {
                // get categories logic
            }
            case "prev" -> {
                // go to previous page logic
            }
            case "next" -> {
                // go to next page logic
            }
            default -> {
                if (input.contains("playlists")) {
                    // get playlists by category logic
                } else {
                    System.out.println("Error: Invalid command.");
                }
            }
        }
    }
}
```

I consider this class to be the main controller because it receives the API responses and prints them for the user to see them.

## Getting authorization

The class InputReader has two attributes that are useful for the authentication and authorization process: `User` and `accessToken`. The variable `user` is used to store the user's access code and its authentication status, the variable `accessToken` is used to store the JWT to get authorization from the spotify API. The authentication logic inside the switch statement is the following.

``` java
Authentication auth = new Authentication();
String authCode = auth.getAccess(user);
user.setCode(authCode);
user.setIsAuthenticated(true);
accessToken = Authentication.getAccessToken(user);
System.out.println("---SUCCESS---");
```

Inside this logic, we instantiate an object of class `Authentication` this class contains two methods: `getAccess()` is used to log in to spotify, and `getAccessToken()` is used to get a token with the authorization to access information from the user's spotify account. 

The authentication class is the following.

``` java
public class Authentication {

    private static String baseUrl = "https://accounts.spotify.com";

    private static final String clientId = "6623a7ef869f41fc9bbd028c510bfe14";

    private static final String clientSecret = "a3d6e766165b4e9bb3f0ad88c27742ed";

    public String getAccess(User user) throws IOException, InterruptedException {
        Printer.printAuth(baseUrl);
        CountDownLatch actionManager = new CountDownLatch(1);
        HttpServer server = HttpServer.create();
        server.bind(new InetSocketAddress(8080), 0);
        server.createContext("/",
                new HttpHandler() {
                    public void handle(HttpExchange exchange) throws IOException {
                        System.out.println("waiting for code...");
                        String query = exchange.getRequestURI().getQuery();
                        String response;
                        int statusCode = 0;
                        if (query != null && query.contains("code")) {
                            System.out.println("code received");
                            user.setCode(query.replace("code=",""));
                            response = "Got the code. Return back to your program.";
                            statusCode = 200;
                            actionManager.countDown();
                        } else {
                            response = "Authorization code not found. Try again.";
                            statusCode = 404;
                        }
                        exchange.sendResponseHeaders(statusCode, response.length());
                        exchange.getResponseBody().write(response.getBytes());
                        exchange.getResponseBody().close();
                    }
                }
        );
        server.start();
        actionManager.await();
        server.stop(1);

        return user.getCode();
    }

    public static String getAccessToken(User user) throws IOException, InterruptedException {
        HttpClient client = HttpClient.newBuilder().build();
        String uri = "/api/token";
        String body = "grant_type=authorization_code" +
                "&code=" + URLEncoder.encode(user.getCode(), StandardCharsets.UTF_8) +
                "&redirect_uri=" + URLEncoder.encode("http://localhost:8080", StandardCharsets.UTF_8);
        String credentials = clientId + ":" + clientSecret;
        String basicAuth = "Basic " + Base64.getEncoder()
                .encodeToString(credentials.getBytes(StandardCharsets.UTF_8));

        HttpRequest request = HttpRequest.newBuilder()
                .header("Content-Type", "application/x-www-form-urlencoded")
                .header("Authorization", basicAuth)
                .uri(URI.create(baseUrl + uri))
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .build();
        System.out.println("making http request for access_token...");

        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        String responseBody = response.body();
        System.out.println(responseBody);

        int startIndex = responseBody.indexOf("\"access_token\":\"") + "\"access_token\":\"".length();
        int endIndex = responseBody.indexOf("\"", startIndex);
        return responseBody.substring(startIndex, endIndex);
    }

    public static void setBaseUrl(String url) {
        baseUrl = url;
    }

}
```

And its purpose is basically to query the spotify API to get permission to access its resources with an access code and an access token. 

## Printing data
Trying to stick to MVC pattern, I placed the printing response logic within the `view` package of my project. This package contains an interface `Printer` with three implementations, one to print Categories, another one to print Albums (new releases), and a last one to print playlists (featured and by category).

``` java
public interface Printer {
    void printContent(List content);
}
```
The only method of this interface receives a generic type `List`, using generic type was useful for printing the required information for whatever type of content we need to print. In the implementation we specify the type of content we are printing.

``` java
public class PlaylistPrinter implements Printer {
    @Override
    public void printContent(List content) {
        if (content == null) return;

        System.out.println("---PLAYLISTS---");
        for (Playlist playlist : (List<Playlist>) content) {
            System.out.println(playlist.getName());
            System.out.println(playlist.getLink() + "\n");
        }
        System.out.println("---PAGE " + Pagination.getCurrentPage() + " OF " + Pagination.getLastPage() + "---");
    }
}
```

``` java
public class CategoriesPrinter implements Printer {
    @Override
    public void printContent(List content) {
        System.out.println("---CATEGORIES---");
        ((List<String>) content).forEach(System.out::println);
        System.out.println("---PAGE " + Pagination.getCurrentPage() + " OF " + Pagination.getLastPage() + "---");
    }
}
```

``` java
public class AlbumPrinter implements Printer{
    @Override
    public void printContent(List content) {
        System.out.println("---NEW RELEASES---");
        for (Album album : (List<Album>) content) {
            System.out.println(album.getName());
            System.out.println(album.getArtists().toString());
            System.out.println(album.getLink() + "\n");
        }
        System.out.println("---PAGE " + Pagination.getCurrentPage() + " OF " + Pagination.getLastPage() + "---");
    }
}
```
The printer interface is declared only once as one of the attributes of `InputReader`, it is assigned to its different implementations based on the user input commands. Using an interface allows us to avoid buch of validations to figure out what type of content the user wants to print when changing pages.

``` java
public class InputReader {
    // other attributes
    private static Printer printer;

    public static void evaluateUserInput(String input) throws IOException, InterruptedException {

        // more code

        switch (input) {
            case "auth" -> {
                // auth logic
            }
            case "featured" -> {
                printer = new PlaylistPrinter();
                // API call logic
                printer.printContent(content);
            }
            case "new" -> {
                printer = new AlbumPrinter();
                // API call logic
                printer.printContent(content);
            }
            case "categories" -> {
                printer = new CategoriesPrinter();
                // API call logic
                printer.printContent(content);
            }
            case "prev" -> {
                // API call logic
                printer.printContent(content);
            }
            case "next" -> {
                // API call logic
                printer.printContent(content);
            }
            default -> {
                if (input.contains("playlists")) {
                    printer = new PlaylistPrinter();
                    // API call logic
                    printer.printContent(content);
                } else {
                    System.out.println("Error: Invalid command.");
                }
            }
        }
    }
}
```

## Spotify API Calls
As API calls were similar and had to be called multiple times from different parts of the code to support pagination, I figured that I could use an approach similar to the one used for printing responses. The problem has some differences, as I needed an interface with pagination control to store abstract paginated content. The pagination control could be standarized for all types of content, so instead of a plain java interface, I used an abstract class interface. This interface contains one abstract method and is implemented differently for each of the application's given actions.

``` java
public abstract class PaginatedContent {

    // pagination control code

    public abstract List fetchPage(String accessToken) throws IOException, InterruptedException;

}
```
This interface and its implementation classes exist inside the model package. Each implementation contains only a call the method that fetches the required data from the spotify API.

Calls to the spotify API are made inside a class called API that exists within the controller package. This class queries the spotify API, processes the API responses, and handles pagination.

``` java
public class API {
    private static String baseUrl = "https://accounts.spotify.com";
    private static final HttpClient client = HttpClient.newBuilder().build();;
    private static HashMap<String, String> categories = new HashMap<>();

    public static List<String> getCategories(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store categories
        // Do API call
        // Process API response
        // Paginate response
    }

    public static void getFeaturedPlaylists(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store featured playlist
        // Do API call
        // Process API response and store it
        // Paginate response
    }

    public static void getNewReleases(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store new releases
        // Do API call
        // Process API response and store it
        // Paginate response
    }

    public static void getPlaylistsByCategory(String accessToken, String categoryName, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store category playlists
        // Update list of categories
        // Validate category name
        // Do API call
        // Process API response and store it
        // Paginate response
    }

    private static void updateCategories(String accessToken) throws IOException, InterruptedException {
        // Reset categories list
        // Do API call
        // Process API response and assign it to categories list
    }

    public static void setBaseUrl(String url) {
        baseUrl = url;
    }

}
```
The logic used for each of the endpoints is super similar, the only differences most of them have is the request URL and the method to process the API response.

### Get Featured playlists
The logic inside the menu's switch statement to get spotify's featured playlists is the following:

``` java
case "featured" -> {
    apiCaller = new PagedFeaturedPlaylists();
    printer = new PlaylistPrinter();
    content = apiCaller.fetchPage(accessToken);
    printer.printContent(content);
}
```

As we saw earlier, the apiCaller variable will store an implementation of the PaginatedContent class, this way the program knows what endpoint of the spotify API it should call when fetching the content from the next or previous pages. The implementation of the `PaginatedContent` interface for this endpoint is the following.

``` java
public class PagedFeaturedPlaylists extends PaginatedContent {
    @Override
    public List<Playlist> fetchPage(String accessToken) throws IOException, InterruptedException {
        return API.getFeaturedPlaylists(accessToken, super.getCurrentPageFirstIndex());
    }
}
```
It uses the interface's pagination control to get the `offset` query parameter used in the API call and calls the required method of the `API` class, the code inside that method is the following.

``` java
    public static List<Playlist> getFeaturedPlaylists(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store all featured playlist
        List<Playlist> featured = new ArrayList<>();

        // Do API call
        final String url = baseUrl + "/v1/browse/featured-playlists";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("Authorization", "Bearer " + accessToken)
                .build();
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

        // process API response and store it
        JsonObject responseJSON = JsonParser.parseString(response.body()).getAsJsonObject();
        JsonObject playlistsObject = responseJSON.getAsJsonObject("playlists");
        JsonArray playlistsArray = playlistsObject.getAsJsonArray("items");

        for (JsonElement item : playlistsArray) {
            JsonObject playlistObject = item.getAsJsonObject();
            String id = playlistObject.get("id").getAsString();
            String name = playlistObject.get("name").getAsString();
            String link = playlistObject.getAsJsonObject("external_urls").get("spotify").getAsString();
            featured.add(new Playlist(id, name, link));
        }

        // paginate response
        int totalElements = playlistsObject.get("total").getAsInt();
        Pagination.setLastPage(totalElements);
        int currentPageLastIndex = Math.min(currentPageFirstIndex + Pagination.getPageSize(), totalElements);
        return featured.subList(currentPageFirstIndex, currentPageLastIndex);
    }
```
This method uses the `Playlist` class to model the playlist information we need, calls the API to get all the featured playlists, maps the response to the `Playlist` model, paginates the response, and calls the printer to print the playlist name and link.

### Get New Releases
The flow to get Spotify's new releases is very similar to the one described above, we assign an instance of `PaginatedContent` with the implementation that makes the corresponding API call to the variable `apiCaller`, we assign an instance of `Printer` with the implementation to print `Album` objects to the variable `printer`, and we call `API.getNewReleases()` with the `fetchPage()` method's implementation. The only differences between `API.getNewReleases()` and `API.getFeaturedPlaylists()` are that the new releases method stores a list of Albums instead of a list of Playlists and the URI to query Spotify's API, other than that, both methods look exactly the same.

### Get Categories
This segment is also very similar to the two described above, with the same differences: another URI and another type for the List's elements, in this case we store `String` objects instead of custom objects.

### Get Playlist by Category
Reading the user input for this command was different from reading the rest of the commands, as it required a dynamic value for the category name. Evaluating something like this in a switch case is impossible, so I stored the processing of that specific input in the default case of the  switch statement:

```java
    default -> {
        if (input.contains("playlists")) {
            String categoryName = input.replace("playlists ", "");
            apiCaller = new PagedCategoryPlaylists(categoryName);
            printer = new PlaylistPrinter();
            content = apiCaller.fetchPage(accessToken);
            printer.printContent(content);
        } else {
            System.out.println("Error: Invalid command.");
        }
    }
```
This checks if the command given by the user contains the string "playlists", deletes that part of the command to keep a string that stores only the name of the category, declares `currentContent` as a `PagedCategoryPlaylists` object, assigns the corresponding instance of `printer`, calls the method that fetches the required data from the API, and prints the response. If the default case is reacehd but the user input does not contain the string "playlists" then an error message will be printed and the app will wait for the next input. 

The logic to make the API call is the most different from the ones described above, starting with the implementation of the interface Paginated content:

``` java
public class PagedCategoryPlaylists implements PaginatedContent {
    private final String categoryName;

    public PagedCategoryPlaylists(String categoryName) {
        this.categoryName = categoryName;
    }

    @Override
    public void fetchPage(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        API.getPlaylistsByCategory(accessToken, this.categoryName, currentPageFirstIndex);
    }
}
```
This class has an additional attribute to store the category name, and a single constructor that requires said name. In the first code block of the API class you could have seen an attribute called categories of type `HashMap<String, String>`, this attribute should store all the categories from the spotify API to validate if the category of the input exists. One could think that it's more logical to use the category id as the key, but because the user inputs the category name and we have to find the category id to build the query URI, a HashMap with the name as key and id as value is the one we require.

``` java
    public static void getPlaylistsByCategory(String accessToken, String categoryName, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        // Initialize variable to store category playlists
        List<Playlist> playlists = new ArrayList<>();

        // Update list of categories and validate category name
        updateCategories(accessToken);
        if (!categories.containsKey(categoryName)) throw new IndexOutOfBoundsException("Unknown category name");
        String categoryId = categories.get(categoryName);

        final String url = baseUrl + "/v1/browse/categories/" + categoryId + "/playlists";

        // Do API call

        try {

            // Process API response and store it

            // Paginate response and return it

        } catch (NullPointerException npe) {
            System.out.println("Error in server response. " + response.body());
        }
    }
```
Other differences in this code is the URI used, and the try-catch statement to evaluate empty responses from the server, this validation is only required for this specific endpoint.

### Pagination
Most of the pagination logic is stored inside the `Pagination` class:

``` java
public class Pagination {

    private static Integer pageSize;
    private static Integer currentPage;
    private static Integer lastPage;
    private Integer currentPageFirstIndex;

    public Pagination() {
        currentPage = 1;
        currentPageFirstIndex = 0;
        lastPage = 1;
    }

    public void nextPage() {
        if ((currentPage + 1) > lastPage) throw new IndexOutOfBoundsException("No more pages");
        currentPage++;
        currentPageFirstIndex += pageSize;
    }

    public void prevPage() {
        if((currentPage - 1) < 1) throw new IndexOutOfBoundsException("No more pages");
        currentPage--;
        currentPageFirstIndex -= pageSize;
    }

    public static void setLastPage(Integer totalElements) {
        lastPage = (int) Math.ceil((double) totalElements / pageSize);
    }

    public Integer getCurrentPageFirstIndex() {
        return currentPageFirstIndex;
    }

    public static Integer getPageSize() {
        return pageSize;
    }

    public static Integer getCurrentPage() {
        return currentPage;
    }

    public static Integer getLastPage() {
        return lastPage;
    }

    protected static void setPageSize(String page) {
        pageSize = Integer.parseInt(page);
    }
}

```
This class is responsible of performing the change of page, validating if the page change is possible, and calculating the number of pages needed to print all the elements of the API response. Other classes can also consult important pagination information, for instance, `getCurrentPageFirstIndex()` method is used when calling every spotify endpoint of the `API` class. 

The rest of the pagination logic is handled in the `PaginatedContent` interface, this interface provides pagination control for its implementations.

``` java
public abstract class PaginatedContent {

    private Pagination pageInfo = new Pagination();

    public Integer getCurrentPageFirstIndex() {
        return pageInfo.getCurrentPageFirstIndex();
    }

    public void changePage(String direction) {
        switch (direction) {
            case "prev" -> pageInfo.prevPage();
            case "next" -> pageInfo.nextPage();
            default -> throw new RuntimeException("Unexpected error changing page");
        }
    }

    public abstract List fetchPage(String accessToken) throws IOException, InterruptedException;

}
```
The user can change the page using `prev` and `next` commands, they are received in the `InputReader` class. Here we use any implementation of our `PaginatedContent` interface to perform the change of page calling the function `changePage()`, this updates the current page and the index of the first element of our current page, then we can query the API with the updated values to receive a new page of content.

``` java
    case "prev" -> {
        apiCaller.changePage("prev");
        content = apiCaller.fetchPage(accessToken);
        printer.printContent(content);
    }
    case "next" -> {
        apiCaller.changePage("next");
        content = apiCaller.fetchPage(accessToken);
        printer.printContent(content);
    }
```
Here, the generalization of the `Printer` implementation assigned for the fetching commands is also useful to print in the required format for each content type.

# ALTERNATIVE SOLUTIONS

## Running the app
Base URLs should exist in a separate configuration file instead of being allocated directly inside the classes that use them. I didn't do it this way because of the size and scope of the project, I wanted to keep the coding really simple and without a lot of files. The method setConfigs() could have also existed on a Utils file to keep the main class super super clean, I did not do it that way because of the way that Hyperskill asked me to advance each part of the project. Every increment of the project required changes in the design of the application, so I wasn't sure of how useful a Utils file would have been.

## Reading user commands
I could have stored all the logic inside the Main class into another class, maybe the InputReader class, and initialize the flow of data there to keep the Main class practically empty, it's a better practice. I did not do it that way because my main class was not that cluttered, and good practices are for projects with either money or a lot of love.

## Getting authorization
I could have stored the access token from the spotify API, the client ID, and the client secret in the User object to keep it object oriented. I did not do it this way because it would result with a lot of information jumping back and forth from one class to another to end up in the same place it started at. The hard-coded values for client ID and client secret could have been placed inside a configuration file aswell. I am aware that this should be done in applications that are not run only locally and have more than one user. 

## Spotify API Calls
The algorithm used in the API class to query the API is very similar for every action. 
1. Initialize variable to store new releases
2. Do API call
3. Process API response and store it
4. Paginate response

There are some things I would have liked to do differently but ended up modifying to pass the Hyperskill's tests. For instance, you can include pagination parameters to get a paginated response from the Spotify API, so I didn't intend to add step 4 into my algorithm. So instead of having something like this

``` java
    public static List<String> getCategories(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        List<String> pagedCategories = new ArrayList<>();

        final String url = baseUrl + "/v1/browse/categories";

        // Do API call

        // process and store API response 

        int totalElements = categoriesObject.get("total").getAsInt();
        Pagination.setLastPage(totalElements);
        int currentPageLastIndex = Math.min(currentPageFirstIndex + Pagination.getPageSize(), totalElements);
        return pagedCategories.subList(currentPageFirstIndex, currentPageLastIndex);
    }
```
I would have liked to have something like this:

``` java
    public static List<String> getCategories(String accessToken, Integer currentPageFirstIndex) throws IOException, InterruptedException {
        List<String> pagedCategories = new ArrayList<>();

        final String url = baseUrl 
                + "/v1/browse/categories?limit=" 
                + Pagination.getPageSize() 
                + "&offset=" 
                + currentPageFirstIndex;

        // Do API call

        // process and store API response 

        int totalElements = categoriesObject.get("total").getAsInt();
        Pagination.setLastPage(totalElements);
        return pagedCategories;
    }
```

Another thing I would have liked to do is to refactor the step 2 (Do API call) into a private function to avoid repeating that code segment in every API call method.

### Get Playlist by Category
I originally intended to have a method called updateCategories() that fetched all categories from the spotify API and a method called getCategories() that fetched a list of paginated categories, but because pagination wasn't working correctly in the mocked API used in the Hyperskill's tests, both of the methods ended up being practically the same and it looks weird in my code :c

## Extra
I think I got way too excited when learning about `static` methods and variables and used them way too much in this code, if I had to re-write it I would try to use the static keyword less.