{% comment %}
  Generates a Github file box from three parameters http://gist-it.appspot.com/

  Usage:
{% endcomment %}
{% assign line = include.line | default: "" %}{% assign footer = include.footer | default: "" %}{% capture git-script-url %}<script src="http://gist-it.appspot.com/{{ include.url }}?slice={{ include.line }}&footer={{ include.footer }}"></script>{% endcapture %}{{ git-script-url | strip_newlines }}
