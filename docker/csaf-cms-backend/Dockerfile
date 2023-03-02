FROM amazoncorretto:17-alpine3.16 AS appbuild
WORKDIR /usr/src
RUN apk add git; \
	git clone https://github.com/secvisogram/csaf-cms-backend.git; \
	cd csaf-cms-backend; \
	sed -i '/vendor = JvmVendorSpec.ADOPTIUM/d' build.gradle; \
	#sed -i 's/languageVersion = JavaLanguageVersion.of(17)/languageVersion = JavaLanguageVersion.of(19)/g' build.gradle; \
	./gradlew build -x test; \
	find . -type f -name 'csaf_cms_backend-?.?.?.jar' -exec mv {} csaf_cms_backend.jar \;

FROM amazoncorretto:17-alpine3.16
RUN wget https://github.com/jgm/pandoc/releases/download/2.18/pandoc-2.18-linux-amd64.tar.gz; \
	tar xvzf pandoc-2.18-linux-amd64.tar.gz --strip-components 1 -C /usr/local/; \
	rm pandoc-2.18-linux-amd64.tar.gz; \
	apk --update --upgrade --no-cache add weasyprint fontconfig ttf-freefont font-noto terminus-font; \
    fc-cache -f \
    && fc-list | sort
WORKDIR /usr/local/bin
COPY --from=appbuild /usr/src/csaf-cms-backend/csaf_cms_backend.jar /usr/local/bin/csaf_cms_backend.jar
VOLUME /var/csaf/
EXPOSE 8081
ENTRYPOINT ["java", "-Xmx2048m", "-jar", "csaf_cms_backend.jar"]
