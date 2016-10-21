---
author: mcleanbyron
ms.assetid: 4920D262-B810-409E-BA3A-F68AADF1B1BC
description: "Используйте примеры кода на языке Java, приведенные в этом разделе, чтобы более подробно ознакомиться с работой API отправки Магазина Windows."
title: "Примеры кода Java для API отправки Магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: b0154cc0669dd97e57ed75fa1f65afa8d23ae0b7
ms.openlocfilehash: 26d5812e41531cf852606227805d0a84255a3f47

---

# Примеры кода Java для API отправки Магазина Windows

В этой статье представлены примеры кода Java для использования *API отправки Магазина Windows*. Дополнительные сведения об этом API-интерфейсе см. в разделе [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md).

В этих примерах кода демонстрируются следующие задачи:

* [Получение маркера доступа Azure AD](java-code-examples-for-the-windows-store-submission-api.md#token).
* [Создание надстройки](java-code-examples-for-the-windows-store-submission-api.md#create-add-on).
* [Создание тестового пакета](java-code-examples-for-the-windows-store-submission-api.md#create-package-flight).
* [Создание и подтверждение отправки приложения](java-code-examples-for-the-windows-store-submission-api.md#create-app-submission).
* [Создание и подтверждение отправки надстройки](java-code-examples-for-the-windows-store-submission-api.md#create-add-on-submission).
* [Создание и подтверждение отправки тестового пакета](java-code-examples-for-the-windows-store-submission-api.md#create-flight-submission).

Вы можете ознакомиться с каждым примером, чтобы подробнее узнать о демонстрируемой в нем задаче, либо вы можете собрать все примеры кода в этой статье в консольное приложение. Полный текст кода см. в разделе [Текст кода](java-code-examples-for-the-windows-store-submission-api.md#code-listing) в конце данной статьи.

## Необходимые условия

В этих примерах используются следующие библиотеки:

* [Apache Commons Logging 1.2](http://commons.apache.org/proper/commons-logging) (commons-logging-1.2.jar).
* [Apache HttpComponents Core 4.4.5 и Apache HttpComponents Client 4.5.2](https://hc.apache.org/) (httpcore-4.4.5.jar и httpclient-4.5.2.jar).
* [JSR 353 JSON Processing API 1.0](https://mvnrepository.com/artifact/javax.json/javax.json-api/1.0) и [JSR 353 JSON Processing Default Provider API 1.0.4](https://mvnrepository.com/artifact/org.glassfish/javax.json/1.0.4) (javax.json-api-1.0.jar и javax.json-1.0.4.jar).

## Главная программа и операторы импорта

В следующем примере показаны операторы import, используемые во всех примерах кода, и реализована программа командной строки, которая вызывает другие примеры методов.

```java
import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URISyntaxException;
import java.text.MessageFormat;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.StatusLine;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpResponseException;
import org.apache.http.client.ResponseHandler;
import org.apache.http.client.methods.*;
import org.apache.http.impl.client.BasicResponseHandler;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.entity.FileEntity;
import org.apache.http.entity.StringEntity;

import javax.json.Json;
import javax.json.JsonObject;
import javax.json.JsonReader;

public class IngestionServiceJavaSamples {

    public static void main(String[] args) throws InterruptedException, URISyntaxException, IOException {

        // Generate access token. Access token is valid for 1 hour. Regenerate access token when needed
        String tenantId = "";//{Your tenant ID}
        String clientId = "";//{Your client ID}
        String clientSecret = "";//{Your client secret}

        String accessToken = GenerateAccessToken(tenantId, clientId, clientSecret);

        // Application submission sample
        String applicationId = "";//{Your application ID}
        String appSubmissionRequestJson = "";//{Your submission request JSON}
        String appSubmissionZipFilePath = "";//{Your zip file path}

        SubmitNewApplicationSubmission(accessToken, applicationId, appSubmissionRequestJson, appSubmissionZipFilePath);

        // Flight samples
        String flightId = "";//{Your flight ID}
        String flightRequestJson = "";//{Your flight request JSON}        
        String flightSubmissionRequestJson = "";//{Your submission request JSON}
        String flightSubmissionZipFilePath = "";//{Your zip file path}    

        CreateNewFlight(accessToken, applicationId, flightRequestJson);
        SubmitNewFlightSubmission(accessToken, applicationId, flightId, flightSubmissionRequestJson, flightSubmissionZipFilePath);

        // In-app-product samples
        String inAppProductId = "";//{Your in-app-product ID}
        String inAppProductRequestJson = "";//{Your in-app-product request JSON}    
        String iapSubmissionRequestJson = "";//{Your submission request JSON}
        String iapSubmissionZipFilePath = "";//{Your zip file path}

        CreateNewInAppProduct(accessToken, inAppProductRequestJson);
        SubmitNewInAppProductSubmission(accessToken, inAppProductId, iapSubmissionRequestJson, iapSubmissionZipFilePath);

    }
}
```

<span id="token" />
## Получение токена доступа Azure AD

В следующем примере показано, как [получить маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token).

```java
public static String GenerateAccessToken(String tenantId, String clientId, String clientSecret) {        
    // Generate access token. Access token is valid for 1 hour. Regenerate access token when needed
    String tokenEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
    String tokenResource = "https://manage.devcenter.microsoft.com";        

    HttpPost tokenRequest = new HttpPost(MessageFormat.format(tokenEndpoint, tenantId));
    String tokenRequestBody = MessageFormat.format("grant_type=client_credentials&client_id={0}&client_secret={1}&resource={2}", clientId, clientSecret, tokenResource);
    tokenRequest.setEntity(new StringEntity(tokenRequestBody, "utf-8"));
    tokenRequest.addHeader("Content-Type", "application/x-www-form-urlencoded; charset=utf-8");

    CloseableHttpClient httpclient = HttpClients.createDefault();
    ResponseHandler<String> responseHandler = new BasicResponseHandler();

    try {
        String tokenResponse = httpclient.execute(tokenRequest, responseHandler);
        JsonReader reader = Json.createReader(new ByteArrayInputStream(tokenResponse.getBytes("UTF-8")));
        String accessToken = reader.readObject().getString("access_token");

        return accessToken;
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }

    return null;
}
```

<span id="create-add-on" />
## Создание надстройки

В следующем примере показано, как [создать и подтвердить новую надстройку](manage-add-ons.md) (надстройки также известны как внутренние продукты приложений или IAP).

```java
public static void CreateNewInAppProduct(String accessToken, String inAppProductRequestJson) throws InterruptedException, IOException {
    CloseableHttpClient httpclient = HttpClients.createDefault();    
    ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

    try {
        // Create a new in-app-product
        HttpPost createInAppProductRequest = new HttpPost("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts");
        createInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
        createInAppProductRequest.addHeader("Content-type", "application/json");
        createInAppProductRequest.addHeader("User-Agent", "Java");
        createInAppProductRequest.setEntity(new StringEntity(inAppProductRequestJson));
        JsonObject createInAppProductResponse = httpclient.execute(createInAppProductRequest, responseHandler);
        String inAppProductId = createInAppProductResponse.getString("id");
        System.out.println(createInAppProductResponse.toString());

        // Delete created in-app-product        
        HttpDelete deleteInAppProductRequest = new HttpDelete("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/" + inAppProductId);
        deleteInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
        deleteInAppProductRequest.addHeader("User-Agent", "Java");
        httpclient.execute(deleteInAppProductRequest, responseHandler);

    } catch (HttpResponseException e){
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}

private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
    ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
        @Override
        public JsonObject handleResponse(
                HttpResponse response) throws IOException {
            StatusLine statusLine = response.getStatusLine();
            HttpEntity entity = response.getEntity();
            JsonObject returnValue = null;
            if(entity != null){
                JsonReader reader = Json.createReader(entity.getContent());
                returnValue = reader.readObject();
            }

            // Log response status and correlation ID
            System.out.println(statusLine);
            System.out.println(response.getFirstHeader("MS-CorrelationId"));

            // Underlying failure reason is in response entity
            if (statusLine.getStatusCode() >= 300) {
                if(entity == null){
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase());
                }
                else{
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase() + " " + returnValue.toString() );
                }
            }

            return returnValue;
        }
    };

    return responseHandler;
}
```

<span id="create-package-flight" />
## Создание тестового пакета

В следующем примере показано, как [создать новый тестовый пакет](manage-flights.md).

```java
public static void CreateNewFlight(String accessToken, String applicationId, String flightRequestJson) throws InterruptedException, IOException {
    CloseableHttpClient httpclient = HttpClients.createDefault();    
    ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

    try {
        // Create a new flight, a flight submission will be created together with the flight
        HttpPost createFlightRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights", applicationId));
        createFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
        createFlightRequest.addHeader("Content-type", "application/json");
        createFlightRequest.setEntity(new StringEntity(flightRequestJson));
        JsonObject createFlightResponse = httpclient.execute(createFlightRequest, responseHandler);

        System.out.println(createFlightResponse.toString());

        String flightId = createFlightResponse.getString("flightId");
        String submissionId = createFlightResponse.getJsonObject("pendingFlightSubmission").getString("id");

        // Delete created flight        
        HttpDelete deleteFlightRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}", applicationId, flightId));
        deleteFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
        httpclient.execute(deleteFlightRequest, responseHandler);

    } catch (HttpResponseException e){
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}

private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
    ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
        @Override
        public JsonObject handleResponse(
                HttpResponse response) throws IOException {
            StatusLine statusLine = response.getStatusLine();
            HttpEntity entity = response.getEntity();
            JsonObject returnValue = null;
            if(entity != null){
                JsonReader reader = Json.createReader(entity.getContent());
                returnValue = reader.readObject();
            }

            // Log response status and correlation ID
            System.out.println(statusLine);
            System.out.println(response.getFirstHeader("MS-CorrelationId"));

            // Underlying failure reason is in response entity
            if (statusLine.getStatusCode() >= 300) {
                if(entity == null){
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase());
                }
                else{
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase() + " " + returnValue.toString() );
                }
            }

            return returnValue;
        }
    };

    return responseHandler;
}
```

<span id="create-app-submission" />
## Создание и подтверждение отправки приложения

В следующем примере показано, как [создать и подтвердить новую отправку приложения](manage-app-submissions.md).

```java
public static void SubmitNewApplicationSubmission(String accessToken, String applicationId, String appSubmissionRequestJson, String zipFilePath) throws InterruptedException, IOException {
    CloseableHttpClient httpclient = HttpClients.createDefault();    
    ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

    try {            
        // Get application
        HttpGet getApplicationRequest = new HttpGet("https://manage.devcenter.microsoft.com/v1.0/my/applications/" + applicationId);
        getApplicationRequest.addHeader("Authorization", "Bearer " + accessToken);
        getApplicationRequest.addHeader("User-Agent", "Java");
        JsonObject getApplicaiontResponse = httpclient.execute(getApplicationRequest, responseHandler);

        // Delete existing in-progress submission
        JsonObject submissionToRemove = getApplicaiontResponse.getJsonObject("pendingApplicationSubmission");
        if(submissionToRemove != null){
            String submissionToRemoveId = submissionToRemove.getString("id");
            HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionToRemoveId));
            deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            deleteSubmissionRequest.addHeader("User-Agent", "Java");
            httpclient.execute(deleteSubmissionRequest, responseHandler);
        }

        // Create submission
        HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions", applicationId));
        createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        createSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);

        String submissionId = createSubmissionResponse.getString("id");
        String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

        // Update submission
        HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionId));
        updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        updateSubmissionRequest.addHeader("Content-type", "application/json");
        updateSubmissionRequest.addHeader("User-Agent", "Java");
        updateSubmissionRequest.setEntity(new StringEntity(appSubmissionRequestJson));
        JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

        // Upload images and packages in a zip file
        UploadZipFile(fileUploadUrl, zipFilePath);

        // Commit submission
        HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}/commit", applicationId, submissionId));
        commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        commitSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
        System.out.println(commitSubmissionResponse.getString("status"));

        // Pull submission status until commit process is completed
        HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionId));
        getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
        getSubmissionStatusRequest.addHeader("User-Agent", "Java");
        JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
        String submissionStatus = getSubmissionStatusResponse.getString("status");
        while(submissionStatus.equals("CommitStarted")){
            Thread.sleep(60*1000);
            getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            submissionStatus = getSubmissionStatusResponse.getString("status");
        }        
        System.out.println(submissionStatus);

    } catch (HttpResponseException e){
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}

private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
    ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
        @Override
        public JsonObject handleResponse(
                HttpResponse response) throws IOException {
            StatusLine statusLine = response.getStatusLine();
            HttpEntity entity = response.getEntity();
            JsonObject returnValue = null;
            if(entity != null){
                JsonReader reader = Json.createReader(entity.getContent());
                returnValue = reader.readObject();
            }

            // Log response status and correlation ID
            System.out.println(statusLine);
            System.out.println(response.getFirstHeader("MS-CorrelationId"));

            // Underlying failure reason is in response entity
            if (statusLine.getStatusCode() >= 300) {
                if(entity == null){
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase());
                }
                else{
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase() + " " + returnValue.toString() );
                }
            }

            return returnValue;
        }
    };

    return responseHandler;
}

private static void UploadZipFile(String fileUploadUrl, String zipFilePath) throws IOException{
    CloseableHttpClient httpclient = HttpClients.createDefault();
    File uploadFile = new File(zipFilePath);
    HttpPut uploadFileRequest = new HttpPut(fileUploadUrl.replace("+", "%2B")); // Encode '+', otherwise it will be decoded as ' '
    uploadFileRequest.addHeader("x-ms-blob-type", "BlockBlob");
    uploadFileRequest.setEntity(new FileEntity(uploadFile));
    CloseableHttpResponse uploadFileResponse;
    try {
        uploadFileResponse = httpclient.execute(uploadFileRequest);
        System.out.println(uploadFileResponse.getStatusLine());
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}
```

<span id="create-add-on-submission" />
## Создание и подтверждение отправки надстройки

В следующем примере показано, как [создать и подтвердить новую отправку надстройки](manage-add-on-submissions.md) (надстройки также известны как внутренние продукты приложений или IAP).

```java
public static void SubmitNewInAppProductSubmission(String accessToken, String inAppProductId, String iapSubmissionRequestJson, String zipFilePath) throws InterruptedException, IOException {
    CloseableHttpClient httpclient = HttpClients.createDefault();    
    ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

    try {
        // Get the in-app-product                    
        HttpGet getInAppProductRequest = new HttpGet("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/" + inAppProductId);
        getInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
        getInAppProductRequest.addHeader("User-Agent", "Java");
        JsonObject getInAppProductResponse = httpclient.execute(getInAppProductRequest, responseHandler);

        // Delete existing in-progress submission
        JsonObject submissionToRemove = getInAppProductResponse.getJsonObject("pendingInAppProductSubmission");
        if(submissionToRemove != null){
            String submissionToRemoveId = submissionToRemove.getString("id");
            HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}", inAppProductId, submissionToRemoveId));
            deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            deleteSubmissionRequest.addHeader("User-Agent", "Java");
            httpclient.execute(deleteSubmissionRequest, responseHandler);
        }            

        // Create submission
        HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions", inAppProductId));
        createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        createSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);
        String submissionId = createSubmissionResponse.getString("id");
        String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

        // Update submission
        HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}", inAppProductId, submissionId));
        updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        updateSubmissionRequest.addHeader("Content-type", "application/json");
        updateSubmissionRequest.setEntity(new StringEntity(iapSubmissionRequestJson));
        JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

        /// Upload images and packages in a zip file
        UploadZipFile(fileUploadUrl, zipFilePath);

        // Commit submission
        HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}/commit", inAppProductId, submissionId));
        commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        commitSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
        System.out.println(commitSubmissionResponse.getString("status"));

        // Pull submission status until commit process is completed
        HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}/status", inAppProductId, submissionId));
        getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
        getSubmissionStatusRequest.addHeader("User-Agent", "Java");
        JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
        String submissionStatus = getSubmissionStatusResponse.getString("status");
        while(submissionStatus.equals("CommitStarted")){
            Thread.sleep(60*1000);
            getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            submissionStatus = getSubmissionStatusResponse.getString("status");
        }        
        System.out.println(submissionStatus);

    } catch (HttpResponseException e){
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}

private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
    ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
        @Override
        public JsonObject handleResponse(
                HttpResponse response) throws IOException {
            StatusLine statusLine = response.getStatusLine();
            HttpEntity entity = response.getEntity();
            JsonObject returnValue = null;
            if(entity != null){
                JsonReader reader = Json.createReader(entity.getContent());
                returnValue = reader.readObject();
            }

            // Log response status and correlation ID
            System.out.println(statusLine);
            System.out.println(response.getFirstHeader("MS-CorrelationId"));

            // Underlying failure reason is in response entity
            if (statusLine.getStatusCode() >= 300) {
                if(entity == null){
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase());
                }
                else{
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase() + " " + returnValue.toString() );
                }
            }

            return returnValue;
        }
    };

    return responseHandler;
}

private static void UploadZipFile(String fileUploadUrl, String zipFilePath) throws IOException{
    CloseableHttpClient httpclient = HttpClients.createDefault();
    File uploadFile = new File(zipFilePath);
    HttpPut uploadFileRequest = new HttpPut(fileUploadUrl.replace("+", "%2B")); // Encode '+', otherwise it will be decoded as ' '
    uploadFileRequest.addHeader("x-ms-blob-type", "BlockBlob");
    uploadFileRequest.setEntity(new FileEntity(uploadFile));
    CloseableHttpResponse uploadFileResponse;
    try {
        uploadFileResponse = httpclient.execute(uploadFileRequest);
        System.out.println(uploadFileResponse.getStatusLine());
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}
```

<span id="create-flight-submission" />
## Создание и подтверждение отправки тестового пакета

В следующем примере показано, как [создать и подтвердить новую отправку тестового пакета](manage-flight-submissions.md).

```java
public static void SubmitNewFlightSubmission(String accessToken, String applicationId, String flightId, String flightSubmissionRequestJson, String zipFilePath) throws InterruptedException, URISyntaxException, IOException {
    CloseableHttpClient httpclient = HttpClients.createDefault();    
    ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

    try {
        // Get flight
        HttpGet getFlightRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}", applicationId, flightId));
        getFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
        getFlightRequest.addHeader("User-Agent", "Java");
        JsonObject getFlightResponse = httpclient.execute(getFlightRequest, responseHandler);

        // Delete existing in-progress submission
        JsonObject submissionToRemove = getFlightResponse.getJsonObject("pendingFlightSubmission");
        if(submissionToRemove != null){
            String submissionToRemoveId = submissionToRemove.getString("id");
            HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionToRemoveId));
            deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            deleteSubmissionRequest.addHeader("User-Agent", "Java");
            httpclient.execute(deleteSubmissionRequest, responseHandler);
        }            

        // Create submission
        HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions", applicationId, flightId));
        createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        createSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);
        String submissionId = createSubmissionResponse.getString("id");
        String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

        // Update submission
        HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionId));
        updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        updateSubmissionRequest.addHeader("Content-type", "application/json");
        updateSubmissionRequest.addHeader("User-Agent", "Java");
        updateSubmissionRequest.setEntity(new StringEntity(flightSubmissionRequestJson));
        JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

        // Upload images and packages in a zip file
        UploadZipFile(fileUploadUrl, zipFilePath);

        // Commit submission
        HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}/commit", applicationId, flightId, submissionId));
        commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
        commitSubmissionRequest.addHeader("User-Agent", "Java");
        JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
        System.out.println(commitSubmissionResponse.getString("status"));

        // Pull submission status until commit process is completed
        HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionId));
        getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
        getSubmissionStatusRequest.addHeader("User-Agent", "Java");
        JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
        String submissionStatus = getSubmissionStatusResponse.getString("status");
        while(submissionStatus.equals("CommitStarted")){
            Thread.sleep(60*1000);
            getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            submissionStatus = getSubmissionStatusResponse.getString("status");
        }        
        System.out.println(submissionStatus);

    } catch (HttpResponseException e){
        e.printStackTrace();
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}

private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
    ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
        @Override
        public JsonObject handleResponse(
                HttpResponse response) throws IOException {
            StatusLine statusLine = response.getStatusLine();
            HttpEntity entity = response.getEntity();
            JsonObject returnValue = null;
            if(entity != null){
                JsonReader reader = Json.createReader(entity.getContent());
                returnValue = reader.readObject();
            }

            // Log response status and correlation ID
            System.out.println(statusLine);
            System.out.println(response.getFirstHeader("MS-CorrelationId"));

            // Underlying failure reason is in response entity
            if (statusLine.getStatusCode() >= 300) {
                if(entity == null){
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase());
                }
                else{
                    throw new HttpResponseException(
                            statusLine.getStatusCode(),
                            statusLine.getReasonPhrase() + " " + returnValue.toString() );
                }
            }

            return returnValue;
        }
    };

    return responseHandler;
}

private static void UploadZipFile(String fileUploadUrl, String zipFilePath) throws IOException{
    CloseableHttpClient httpclient = HttpClients.createDefault();
    File uploadFile = new File(zipFilePath);
    HttpPut uploadFileRequest = new HttpPut(fileUploadUrl.replace("+", "%2B")); // Encode '+', otherwise it will be decoded as ' '
    uploadFileRequest.addHeader("x-ms-blob-type", "BlockBlob");
    uploadFileRequest.setEntity(new FileEntity(uploadFile));
    CloseableHttpResponse uploadFileResponse;
    try {
        uploadFileResponse = httpclient.execute(uploadFileRequest);
        System.out.println(uploadFileResponse.getStatusLine());
    } catch (ClientProtocolException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        httpclient.close();
    }
}
```

<span id="code-listing" />
## Полный текст кода

В приведенном ниже тексте кода содержатся все предыдущие примеры, собранный в один исходный файл.

```java
import java.io.ByteArrayInputStream;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URISyntaxException;
import java.text.MessageFormat;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.StatusLine;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.HttpResponseException;
import org.apache.http.client.ResponseHandler;
import org.apache.http.client.methods.*;
import org.apache.http.impl.client.BasicResponseHandler;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.entity.FileEntity;
import org.apache.http.entity.StringEntity;

import javax.json.Json;
import javax.json.JsonObject;
import javax.json.JsonReader;

public class IngestionServiceJavaSamples {

    public static void main(String[] args) throws InterruptedException, URISyntaxException, IOException {

        // Generate access token. Access token is valid for 1 hour. Regenerate access token when needed
        String tenantId = "";//{Your tenant ID}
        String clientId = "";//{Your client ID}
        String clientSecret = "";//{Your client secret}

        String accessToken = GenerateAccessToken(tenantId, clientId, clientSecret);

        // Application submission sample
        String applicationId = "";//{Your application ID}
        String appSubmissionRequestJson = "";//{Your submission request JSON}
        String appSubmissionZipFilePath = "";//{Your zip file path}

        SubmitNewApplicationSubmission(accessToken, applicationId, appSubmissionRequestJson, appSubmissionZipFilePath);

        // Flight samples
        String flightId = "";//{Your flight ID}
        String flightRequestJson = "";//{Your flight request JSON}        
        String flightSubmissionRequestJson = "";//{Your submission request JSON}
        String flightSubmissionZipFilePath = "";//{Your zip file path}    

        CreateNewFlight(accessToken, applicationId, flightRequestJson);
        SubmitNewFlightSubmission(accessToken, applicationId, flightId, flightSubmissionRequestJson, flightSubmissionZipFilePath);

        // In-app-product samples
        String inAppProductId = "";//{Your in-app-product ID}
        String inAppProductRequestJson = "";//{Your in-app-product request JSON}    
        String iapSubmissionRequestJson = "";//{Your submission request JSON}
        String iapSubmissionZipFilePath = "";//{Your zip file path}

        CreateNewInAppProduct(accessToken, inAppProductRequestJson);
        SubmitNewInAppProductSubmission(accessToken, inAppProductId, iapSubmissionRequestJson, iapSubmissionZipFilePath);

    }

    public static String GenerateAccessToken(String tenantId, String clientId, String clientSecret) {        
        // Generate access token. Access token is valid for 1 hour. Regenerate access token when needed
        String tokenEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
        String tokenResource = "https://manage.devcenter.microsoft.com";        

        HttpPost tokenRequest = new HttpPost(MessageFormat.format(tokenEndpoint, tenantId));
        String tokenRequestBody = MessageFormat.format("grant_type=client_credentials&client_id={0}&client_secret={1}&resource={2}", clientId, clientSecret, tokenResource);
        tokenRequest.setEntity(new StringEntity(tokenRequestBody, "utf-8"));
        tokenRequest.addHeader("Content-Type", "application/x-www-form-urlencoded; charset=utf-8");

        CloseableHttpClient httpclient = HttpClients.createDefault();
        ResponseHandler<String> responseHandler = new BasicResponseHandler();

        try {
            String tokenResponse = httpclient.execute(tokenRequest, responseHandler);
            JsonReader reader = Json.createReader(new ByteArrayInputStream(tokenResponse.getBytes("UTF-8")));
            String accessToken = reader.readObject().getString("access_token");

            return accessToken;
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return null;
    }

    public static void SubmitNewApplicationSubmission(String accessToken, String applicationId, String appSubmissionRequestJson, String zipFilePath) throws InterruptedException, IOException {
        CloseableHttpClient httpclient = HttpClients.createDefault();    
        ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

        try {            
            // Get application
            HttpGet getApplicationRequest = new HttpGet("https://manage.devcenter.microsoft.com/v1.0/my/applications/" + applicationId);
            getApplicationRequest.addHeader("Authorization", "Bearer " + accessToken);
            getApplicationRequest.addHeader("User-Agent", "Java");
            JsonObject getApplicaiontResponse = httpclient.execute(getApplicationRequest, responseHandler);

            // Delete existing in-progress submission
            JsonObject submissionToRemove = getApplicaiontResponse.getJsonObject("pendingApplicationSubmission");
            if(submissionToRemove != null){
                String submissionToRemoveId = submissionToRemove.getString("id");
                HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionToRemoveId));
                deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
                deleteSubmissionRequest.addHeader("User-Agent", "Java");
                httpclient.execute(deleteSubmissionRequest, responseHandler);
            }

            // Create submission
            HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions", applicationId));
            createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            createSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);

            String submissionId = createSubmissionResponse.getString("id");
            String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

            // Update submission
            HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionId));
            updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            updateSubmissionRequest.addHeader("Content-type", "application/json");
            updateSubmissionRequest.addHeader("User-Agent", "Java");
            updateSubmissionRequest.setEntity(new StringEntity(appSubmissionRequestJson));
            JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

            // Upload images and packages in a zip file
            UploadZipFile(fileUploadUrl, zipFilePath);

            // Commit submission
            HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}/commit", applicationId, submissionId));
            commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            commitSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
            System.out.println(commitSubmissionResponse.getString("status"));

            // Pull submission status until commit process is completed
            HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/submissions/{1}", applicationId, submissionId));
            getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
            getSubmissionStatusRequest.addHeader("User-Agent", "Java");
            JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            String submissionStatus = getSubmissionStatusResponse.getString("status");
            while(submissionStatus.equals("CommitStarted")){
                Thread.sleep(60*1000);
                getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
                submissionStatus = getSubmissionStatusResponse.getString("status");
            }        
            System.out.println(submissionStatus);

        } catch (HttpResponseException e){
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    public static void CreateNewFlight(String accessToken, String applicationId, String flightRequestJson) throws InterruptedException, IOException {
        CloseableHttpClient httpclient = HttpClients.createDefault();    
        ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

        try {
            // Create a new flight, a flight submission will be created together with the flight
            HttpPost createFlightRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights", applicationId));
            createFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
            createFlightRequest.addHeader("Content-type", "application/json");
            createFlightRequest.setEntity(new StringEntity(flightRequestJson));
            JsonObject createFlightResponse = httpclient.execute(createFlightRequest, responseHandler);

            System.out.println(createFlightResponse.toString());

            String flightId = createFlightResponse.getString("flightId");
            String submissionId = createFlightResponse.getJsonObject("pendingFlightSubmission").getString("id");

            // Delete created flight        
            HttpDelete deleteFlightRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}", applicationId, flightId));
            deleteFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
            httpclient.execute(deleteFlightRequest, responseHandler);

        } catch (HttpResponseException e){
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    public static void SubmitNewFlightSubmission(String accessToken, String applicationId, String flightId, String flightSubmissionRequestJson, String zipFilePath) throws InterruptedException, URISyntaxException, IOException {
        CloseableHttpClient httpclient = HttpClients.createDefault();    
        ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

        try {
            // Get flight
            HttpGet getFlightRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}", applicationId, flightId));
            getFlightRequest.addHeader("Authorization", "Bearer " + accessToken);
            getFlightRequest.addHeader("User-Agent", "Java");
            JsonObject getFlightResponse = httpclient.execute(getFlightRequest, responseHandler);

            // Delete existing in-progress submission
            JsonObject submissionToRemove = getFlightResponse.getJsonObject("pendingFlightSubmission");
            if(submissionToRemove != null){
                String submissionToRemoveId = submissionToRemove.getString("id");
                HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionToRemoveId));
                deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
                deleteSubmissionRequest.addHeader("User-Agent", "Java");
                httpclient.execute(deleteSubmissionRequest, responseHandler);
            }            

            // Create submission
            HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions", applicationId, flightId));
            createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            createSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);
            String submissionId = createSubmissionResponse.getString("id");
            String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

            // Update submission
            HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionId));
            updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            updateSubmissionRequest.addHeader("Content-type", "application/json");
            updateSubmissionRequest.addHeader("User-Agent", "Java");
            updateSubmissionRequest.setEntity(new StringEntity(flightSubmissionRequestJson));
            JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

            // Upload images and packages in a zip file
            UploadZipFile(fileUploadUrl, zipFilePath);

            // Commit submission
            HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}/commit", applicationId, flightId, submissionId));
            commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            commitSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
            System.out.println(commitSubmissionResponse.getString("status"));

            // Pull submission status until commit process is completed
            HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/applications/{0}/flights/{1}/submissions/{2}", applicationId, flightId, submissionId));
            getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
            getSubmissionStatusRequest.addHeader("User-Agent", "Java");
            JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            String submissionStatus = getSubmissionStatusResponse.getString("status");
            while(submissionStatus.equals("CommitStarted")){
                Thread.sleep(60*1000);
                getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
                submissionStatus = getSubmissionStatusResponse.getString("status");
            }        
            System.out.println(submissionStatus);

        } catch (HttpResponseException e){
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    public static void CreateNewInAppProduct(String accessToken, String inAppProductRequestJson) throws InterruptedException, IOException {
        CloseableHttpClient httpclient = HttpClients.createDefault();    
        ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

        try {
            // Create a new in-app-product
            HttpPost createInAppProductRequest = new HttpPost("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts");
            createInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
            createInAppProductRequest.addHeader("Content-type", "application/json");
            createInAppProductRequest.addHeader("User-Agent", "Java");
            createInAppProductRequest.setEntity(new StringEntity(inAppProductRequestJson));
            JsonObject createInAppProductResponse = httpclient.execute(createInAppProductRequest, responseHandler);
            String inAppProductId = createInAppProductResponse.getString("id");
            System.out.println(createInAppProductResponse.toString());

            // Delete created in-app-product        
            HttpDelete deleteInAppProductRequest = new HttpDelete("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/" + inAppProductId);
            deleteInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
            deleteInAppProductRequest.addHeader("User-Agent", "Java");
            httpclient.execute(deleteInAppProductRequest, responseHandler);

        } catch (HttpResponseException e){
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    public static void SubmitNewInAppProductSubmission(String accessToken, String inAppProductId, String iapSubmissionRequestJson, String zipFilePath) throws InterruptedException, IOException {
        CloseableHttpClient httpclient = HttpClients.createDefault();    
        ResponseHandler<JsonObject> responseHandler = CreateJsonResponseHandler();

        try {
            // Get the in-app-product                    
            HttpGet getInAppProductRequest = new HttpGet("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/" + inAppProductId);
            getInAppProductRequest.addHeader("Authorization", "Bearer " + accessToken);
            getInAppProductRequest.addHeader("User-Agent", "Java");
            JsonObject getInAppProductResponse = httpclient.execute(getInAppProductRequest, responseHandler);

            // Delete existing in-progress submission
            JsonObject submissionToRemove = getInAppProductResponse.getJsonObject("pendingInAppProductSubmission");
            if(submissionToRemove != null){
                String submissionToRemoveId = submissionToRemove.getString("id");
                HttpDelete deleteSubmissionRequest = new HttpDelete(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}", inAppProductId, submissionToRemoveId));
                deleteSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
                deleteSubmissionRequest.addHeader("User-Agent", "Java");
                httpclient.execute(deleteSubmissionRequest, responseHandler);
            }            

            // Create submission
            HttpPost createSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions", inAppProductId));
            createSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            createSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject createSubmissionResponse = httpclient.execute(createSubmissionRequest, responseHandler);
            String submissionId = createSubmissionResponse.getString("id");
            String fileUploadUrl = createSubmissionResponse.getString("fileUploadUrl");

            // Update submission
            HttpPut updateSubmissionRequest = new HttpPut(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}", inAppProductId, submissionId));
            updateSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            updateSubmissionRequest.addHeader("Content-type", "application/json");
            updateSubmissionRequest.setEntity(new StringEntity(iapSubmissionRequestJson));
            JsonObject updateSubmissionResponse = httpclient.execute(updateSubmissionRequest, responseHandler);

            /// Upload images and packages in a zip file
            UploadZipFile(fileUploadUrl, zipFilePath);

            // Commit submission
            HttpPost commitSubmissionRequest = new HttpPost(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}/commit", inAppProductId, submissionId));
            commitSubmissionRequest.addHeader("Authorization", "Bearer " + accessToken);
            commitSubmissionRequest.addHeader("User-Agent", "Java");
            JsonObject commitSubmissionResponse = httpclient.execute(commitSubmissionRequest, responseHandler);
            System.out.println(commitSubmissionResponse.getString("status"));

            // Pull submission status until commit process is completed
            HttpGet getSubmissionStatusRequest = new HttpGet(MessageFormat.format("https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{0}/submissions/{1}/status", inAppProductId, submissionId));
            getSubmissionStatusRequest.addHeader("Authorization", "Bearer " + accessToken);
            getSubmissionStatusRequest.addHeader("User-Agent", "Java");
            JsonObject getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
            String submissionStatus = getSubmissionStatusResponse.getString("status");
            while(submissionStatus.equals("CommitStarted")){
                Thread.sleep(60*1000);
                getSubmissionStatusResponse = httpclient.execute(getSubmissionStatusRequest, responseHandler);
                submissionStatus = getSubmissionStatusResponse.getString("status");
            }        
            System.out.println(submissionStatus);

        } catch (HttpResponseException e){
            e.printStackTrace();
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    private static void UploadZipFile(String fileUploadUrl, String zipFilePath) throws IOException{
        CloseableHttpClient httpclient = HttpClients.createDefault();
        File uploadFile = new File(zipFilePath);
        HttpPut uploadFileRequest = new HttpPut(fileUploadUrl.replace("+", "%2B")); // Encode '+', otherwise it will be decoded as ' '
        uploadFileRequest.addHeader("x-ms-blob-type", "BlockBlob");
        uploadFileRequest.setEntity(new FileEntity(uploadFile));
        CloseableHttpResponse uploadFileResponse;
        try {
            uploadFileResponse = httpclient.execute(uploadFileRequest);
            System.out.println(uploadFileResponse.getStatusLine());
        } catch (ClientProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            httpclient.close();
        }
    }

    private static ResponseHandler<JsonObject> CreateJsonResponseHandler(){
        ResponseHandler<JsonObject> responseHandler = new ResponseHandler<JsonObject>() {
            @Override
            public JsonObject handleResponse(
                    HttpResponse response) throws IOException {
                StatusLine statusLine = response.getStatusLine();
                HttpEntity entity = response.getEntity();
                JsonObject returnValue = null;
                if(entity != null){
                    JsonReader reader = Json.createReader(entity.getContent());
                    returnValue = reader.readObject();
                }

                // Log response status and correlation ID
                System.out.println(statusLine);
                System.out.println(response.getFirstHeader("MS-CorrelationId"));

                // Underlying failure reason is in response entity
                if (statusLine.getStatusCode() >= 300) {
                    if(entity == null){
                        throw new HttpResponseException(
                                statusLine.getStatusCode(),
                                statusLine.getReasonPhrase());
                    }
                    else{
                        throw new HttpResponseException(
                                statusLine.getStatusCode(),
                                statusLine.getReasonPhrase() + " " + returnValue.toString() );
                    }
                }

                return returnValue;
            }
        };

        return responseHandler;
    }
}
```

## Связанные разделы

* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)



<!--HONumber=Aug16_HO5-->


