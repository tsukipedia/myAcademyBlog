---
category: Logs
title: Open Source Software - OpenRefine PR 2
date: 2023-07-15
---

# OVERVIEW
The issue at hand in the OpenRefine project involves a crash in the OpenRefine server when attempting to edit the customMetadata field in the about section of a project. This crash occurs both when manually interacting with the user interface and when using the OpenRefine API's set-project-metadata endpoint. As a result, any newly added values in the customMetadata field are not successfully saved and are lost upon page reload. To address this, a solution was devised to handle the field mapping correctly and ensure the successful persistence of custom metadata.

# CONTEXT
OpenRefine is an open source tool designed for working with messy data, providing functionalities to clean, transform, and refine datasets. To work with data in OpenRefine, it is necessary to store it in a project, and each project has associated metadata. The customMetadata field within the project's about section allows users to add additional information in a JSON-like format.

The reported issue occurs when attempting to edit the customMetadata field. When trying to add new values, the server crashes because of a java.lang.IllegalArgumentException. The error message indicates an incompatible assignment, specifically related to the mapping between the java.util.Map field _customMetadata and a java.lang.String.

The problem affects both manual interactions with the user interface and API-based interactions using the set-project-metadata endpoint. The expected behavior is that the newly added values in the customMetadata field should be saved and persist when the page is reloaded.

Throughout the problem-solving process, careful analysis of the server crash and its underlying cause was conducted. By examining the relevant code sections and understanding the expected behavior, a solution was devised to rectify the issue and ensure the seamless functioning of the customMetadata field.

# SOLUTION
To address the underlying cause of the crash, a comprehensive solution was developed that involved implementing proper field mapping and handling within the codebase of OpenRefine. The focus was specifically on resolving the issue related to the customMetadata field, ensuring that it is correctly handled as a Map rather than a String. By successfully implementing this solution, users of OpenRefine will be able to add and save custom metadata values without encountering server crashes.

The core of the solution lies within the `setAnyField()` method located in the ProjectMetadata class. This method handles the dynamic assignment of field values based on the provided metaName and valueString parameters. In the case of the customMetadata field, the solution utilizes OpenRefine's singleton instance of an ObjectMapper, which is an essential component of their parsing utilities. This `ObjectMapper` is responsible for deserializing the JSON-like valueString into a Map<String, Object>, which can then be assigned to the customMetadata field.

```java
public void setAnyField(String metaName, String valueString) throws JsonMappingException, JsonProcessingException {
        Class<? extends ProjectMetadata> metaClass = this.getClass();
        try {
            Field metaField = metaClass.getDeclaredField("_" + metaName);
            if (metaName.equals("tags")) {
                metaField.set(this, valueString.split(","));
            } else {
+           } else if (metaName.equals("customMetadata")) {
+               Map<String, Object> map = ParsingUtilities.mapper.readValue(valueString, HashMap.class);
+               metaField.set(this, map);
            } else {
                metaField.set(this, valueString);
            }
+       } catch (JsonProcessingException e) {
+           String errorMessage = "Error reading JSON: " + e.getOriginalMessage();
+           logger.error(errorMessage);
+           throw new IllegalArgumentException(errorMessage);
        } catch (NoSuchFieldException e) {
            updateUserMetadata(metaName, valueString);
        } catch (SecurityException | IllegalArgumentException | IllegalAccessException e) {
            logger.error(ExceptionUtils.getFullStackTrace(e));
            throw new RuntimeException(e);
        }
    }
```
Knowing the project's structure was necessary to implement this solution, particularly in relation to the utilization of OpenRefine's singleton instance of an ObjectMapper. The ObjectMapper plays a crucial role in the project's parsing utilities, as data mapping is a fundamental aspect of data analysis software. Additionally, by incorporating new tests specifically tailored to the customMetadata field, the solution not only resolved the crash issue but also ensured the continued functionality and reliability of this component within the project.

# ALTERNATIVE SOLUTIONS
It's best to let exceptions propagate up to a high level of your application, and handle logging and user messaging there. I did not follow this standard to keep consistency with the codebase.
``` java
    } catch (JsonProcessingException e) {
        String errorMessage = "Error reading JSON: " + e.getOriginalMessage();
        throw new IllegalArgumentException(errorMessage, e);
    } catch (NoSuchFieldException e) {
        updateUserMetadata(metaName, valueString);
    } catch (SecurityException | IllegalArgumentException | IllegalAccessException e) {
        throw new RuntimeException(e);
    }
```
If you have several possible values for a variable, it's cleaner to use a switch statement instead of multiple if-else conditions. I used if-else conditions to keep consistency.
``` java
public void setAnyField(String metaName, String valueString) 
        throws JsonMappingException, JsonProcessingException {
    Class<? extends ProjectMetadata> metaClass = this.getClass();
    try {
        Field metaField = metaClass.getDeclaredField("_" + metaName);
        switch (metaName) {
            case "tags":
                metaField.set(this, valueString.split(","));
                break;
            case "customMetadata":
                Map<String, Object> map = ParsingUtilities.mapper.readValue(valueString, HashMap.class);
                metaField.set(this, map);
                break;
            default:
                metaField.set(this, valueString);
                break;
        }
```

