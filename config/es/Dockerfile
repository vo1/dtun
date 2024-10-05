FROM docker.elastic.co/elasticsearch/elasticsearch:7.12.0
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin remove analysis-icu; exit 0;
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin remove analysis-phonetic; exit 0;
RUN /usr/share/elasticsearch/bin/elasticsearch-plugin install --batch analysis-icu analysis-phonetic