FROM joernhees/virtuoso
MAINTAINER Magnus Knuth

RUN \
	rm -rf /var/lib/virtuoso-opensource-7/* \
	&& 	cp -a /var/lib/virtuoso-opensource-7.orig/* /var/lib/virtuoso-opensource-7/ \
	&& /etc/init.d/virtuoso-opensource-7 start \
	&& /usr/local/sbin/wait_ready \

	# install some VAD packages for DBpedia into our db which we'll keep in db_dir
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=vad_install('/usr/share/virtuoso-opensource-7/vad/rdf_mappers_dav.vad');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=vad_install('/usr/share/virtuoso-opensource-7/vad/dbpedia_dav.vad');" \

	# setting registry values
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=uptime();" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_get_all();" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_decode_iri','on');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_domain','http://nl.dbpedia.org');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_graph', 'http://nl.dbpedia.org');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_lang', 'nl');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_DynamicLocal', 'off');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_category', 'Categorie');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_imprint', 'http://nl.dbpedia.org/web/contact');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_website','http://nl.dbpedia.org/');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_lhost', ':80');" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=registry_set('dbp_vhost','nl.dbpedia.org');" \

	# set index mode to manual
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=DB.DBA.VT_BATCH_UPDATE ('DB.DBA.RDF_OBJ', 'ON', NULL);" \
	&& isql-vt PROMPT=OFF VERBOSE=OFF BANNER=OFF "EXEC=SPARQL CLEAR GRAPH <http://nl.dbpedia.org>;" \


	&& 	/etc/init.d/virtuoso-opensource-7 stop \
	# sadly the above doesn't really wait for DB shutdown... give it 30 more seconds
	&& sleep 30 \

	# back init state up to init empty mounted DB volume in start.sh
	&& rm -rf /var/lib/virtuoso-opensource-7.orig/* \
	&& cp -a /var/lib/virtuoso-opensource-7/* /var/lib/virtuoso-opensource-7.orig/ \
	&& ls -a /var/lib/virtuoso-opensource-7.orig/

VOLUME "/var/lib/virtuoso-opensource-7" "/import"

EXPOSE 1111 8890

# you can override the following default values via env vars and start.sh will
# replace them in the virtuoso.ini:
# ENV NumberOfBuffers=10000 MaxDirtyBuffers=6000 MaxCheckpointRemap=2000

ENTRYPOINT ["/usr/local/sbin/start.sh"]
