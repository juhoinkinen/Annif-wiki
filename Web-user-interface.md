Annif includes a simple web user interface mainly intended for testing models. The web UI can be started using the command `annif run`, which runs a small web server intended for development. It can also be accessed as the root URL when Annif is [[run as a WSGI service|Running as a WSGI service]].

The UI consists of a form where you can select a project and enter (or paste) text. Clicking the Suggest button will present a list of suggested subjects along with their score values.

There are buttons for selecting the maximum number of suggested subjects, and a collapsible section containing information about the selected project.

[[/images/annif-web-ui.png|Annif web UI screenshot]]

The UI also includes a link to the interactive OpenAPI documentation that can be used to inspect and test the REST API.