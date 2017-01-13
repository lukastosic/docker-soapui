# SoapUI in docker

## Intro
This Docker image is based on alpine jre-8 and contains SoapUI installation.

## Who is this image for?
Use this image if you need docker way to execute SoapUI tests (if that is your develpoment or CI process).

## How to use
In this docker image SoapUI application ``testrunner.sh`` is set as ``ENTRYPOINT``. 

This means that as soon as you ``run`` the container will start ``testrunner.sh``, so you must provide proper parameters depending how you want to run it.

List of all CLI parameters to be used with ``testrunner``: [CLI parameters](https://www.soapui.org/test-automation/running-functional-tests.html)

### Adding test project .xml files

You can alter ``Dockerfile`` and ``ADD`` or ``COPY`` your test project ``.xml`` file, or you can map volume from host system that contains ``.xml`` file to folder ``/opt/soapui/projects`` in container. This will make file available from container to be picked up.

### Saving test results

Again, data inside container is lost when container is removed, so if you want also to save test results, you have to map appropriate volume on your host system to tests output folder. Default output folder is ``/opt/soapui/projects/testresult``, but you can also specify output folder using ``testrunner.sh`` CLI parameters.

### Sample run command structure

Sample ``docker run`` command structure:

```
docker run -i --rm \
	-v <folder_with_project_xml_file>:/opt/soapui/projects \
	-v <folder_to_save_test_results>:/opt/soapui/projects/testresult \
	lukastosic/docker-soapui:<version> \
	-e<endpoint_to_test> \
	-s"<name_of_testsuite>" \
	-r -j \
	-f<path_to_results_folder> \
	-I "<path_to_project_test_xml_file>"	
```

#### First section - docker run parameters

``docker run -i --rm`` will run docker container, print output on terminal (``-i`` parameter) and remove container and **non-named** volumes (``--rm`` parameter). [Docker ``run`` reference](https://docs.docker.com/engine/reference/run)

``-v`` is setting volumes between container and host system

``lukastosic/docker-soapui:<version>`` will take appropriate version of docker image from docker hub. You don't have to use ``:<version>`` section - it will use ``latest`` by default

#### Second section - testrunner.sh parameters

List of all CLI parameters: [CLI parameters](https://www.soapui.org/test-automation/running-functional-tests.html)

``-e`` Provides endpoint against which to run tests

``-s`` name of the test suite to run

``-r`` Print out summary at the end of the test

``-j`` Save test results in JUnit format

``-f`` test results output path

``-I`` path to input xml file

### Sample run command

```
docker run -i --rm \
	-v /myproject:/opt/soapui/projects \
	-v /myproject/result:/opt/soapui/projects/testresult \
	lukastosic/docker-soapui \
	-ehttp://mymuleproject:8080/mycoolapp \
	-s"MyTestSuite" \
	-r -j \
	-f/opt/soapui/projects/testresult \
	-I "/opt/soapui/projects/mytestproject.xml"
```

End result of this command is output on terminal with logging and result of SoapUI ``testrunner`` and saving test resuls in mapped folder on host system. Docker container that was started with this command will be auto-deleted.

#### Run variations

You can use ``-d`` parameter in ``docker run`` to detach terminal (run in background if you don't want log output on screen)

## Usage with Jenkins (CI)

Jenkins has (multiple) JUnit processing plugins.

You can collect test run results in JUnit format (``testrunner.sh`` with ``-j`` CLI parameter) and show them in Jenkins.

## SoapUI tester as a web-server image

Alternatively, you can use different Docker image: https://hub.docker.com/r/ddavison/soapui/

That image also contains tiny web server that runs on top of SoapUI that accepts commands, runs tests and responds with HTTP codes about test results.

## Thanks to:

- Dj(ddavison): https://github.com/ddavison and his docker soap ui project: https://github.com/ddavison/docker-soapui
- Phong Nhu (INFOdation)
- Hugo Pragt (INFOdation)
