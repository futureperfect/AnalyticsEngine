---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-18"

subcollection: AnalyticsEngine

---

<!-- Attribute definitions -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Uploading files to HDFS
{: #upload-files-hdfs}

You can choose to upload your data in HDFS or an object store. Data can be loaded into HDFS by using the:

- [HDFS CLI](#uploading-your-data-by-using-the-hdfs-cli)
- [WebHDFS API](#uploading-your-data-by-using-the-webhdfs-rest-api)

For sensitive data, you should use a secure location that is previously created in HDFS.

You can upload files to your home directory (`/user/clsadmin`). However, you should avoid uploading files to the `/tmp` directory.

## Uploading your data by using the HDFS CLI

To use the HDFS CLI, you must use SSH to log in to the cluster using the credentials.

**Prerequisite**: You need your user credentials and the SSH endpoint. See See [Retrieving service endpoints](/docs/services/AnalyticsEngine?topic=AnalyticsEngine-retrieve-endpoints).

You access the HDFS CLI by using the HDFS command. Refer to the following examples for using the HDFS CLI:

- **Creating an empty directory**

 To create a directory under the user home:

 ```hdfs dfs –mkdir –p /user/clsadmin/test-dir```

- **Uploading a file to HDFS**

 To upload a file to an existing HDFS directory:

 ```hdfs dfs –put test-file /user/clsadmin/test-dir```

- **Deleting a file/directory from HDFS**

 To delete file/directory from HDFS:

 ```hdfs dfs –rm –f /user/clsadmin/test-dir```

## Uploading your data by using the WebHDFS REST API

For programmatic access to the HDFS, use the WebHDFS REST API.

**Prerequisites:** You need your user credentials and the WebHDFS URL.  See [Retrieving service endpoints](/docs/services/AnalyticsEngine?topic=AnalyticsEngine-retrieve-endpoints).

To upload data to HDFS by using the WebHDFS REST API:

1. Open a command prompt.

1. Change directory to the location of the data files that you want to upload. Using the WebHDFS URL that is identified by checking the docs as described under Prerequisites above, make a REST API call by using the cURL command to show directory contents, create directories, and upload files.

 For example, to show the current contents of your cluster's top-level HDFS directory, which is named `/user`, run the following command:

 ```curl -i -s --user clsadmin:your_password --max-time 45 \
 https://XXXXX:8443/gateway/default/webhdfs/v1/user?op=LISTSTATUS```

 The value of XXXXX is the host name of your cluster retrieved from the service endpoints json. If the call completes successfully, JSON returns `200 OK`.

1. To upload a file, run the following command:

 ```curl -i -L -s --user clsadmin:your_password --max-time 45 -X PUT -T file_name.txt \
 https://XXXXX:8443/gateway/default/webhdfs/v1/user/clsadmin/path_to_file/file_name?op=CREATE```

 If the directories do not exist, they are created. If the call completes successfully, JSON returns `201 Created`.

 Run one cURL command for each file that you want to upload.

1. To create an empty directory, for example an output directory, run the following command:
```curl -i  -s --user clsadmin:your_password --max-time 45 -X PUT
   https://XXXXX:8443/gateway/default/webhdfs/v1/user/clsadmin/path_to_directory?op=MKDIRS ```

1. To remove a file, run the following command:

 ```curl -i -s --user clsadmin:your_password --max-time 45 -X DELETE
   https://XXXXX:8443/gateway/default/webhdfs/v1/user/clsadmin/path_to_file?op=DELETE
   ```

 You can't remove a directory that isn't empty.


An alternative way to look at the directory structure, contents, owners, and size is to navigate to the following URL:

```
https://<changeme>:8443/gateway/default/hdfs/explorer.html
```
where `<changeme>`  is the URL to the cluster. For example, for data on a cluster in the US-South region, use:
```
https://XXXXX.us-south.ae.appdomain.cloud:8443/gateway/default/hdfs/explorer.html
```
## Code samples

Here is a code sample that uses the PyWebHdfsClient, a Python wrapper for the Hadoop WebHDFS REST API to upload data to HDFS:

```python
from pywebhdfs.webhdfs import PyWebHdfsClient

hdfs = PyWebHdfsClient(
   base_uri_pattern="https://chs-abh-007-mn001.us-south.ae.appdomain.cloud:8443/gateway/default/webhdfs/v1/",
   request_extra_opts={'verify': False,'auth': ('clsadmin', 'itsasecret')}
)

my_data = 'Hello World'
my_file = '/user/clsadmin/myfile.txt'
hdfs.create_file(my_file, my_data) ```

## Learn more

[Apache Hadoop - WebHDFS REST API documentation](https://hadoop.apache.org/docs/r3.1.0/hadoop-project-dist/hadoop-hdfs/WebHDFS.html)
