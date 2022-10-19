PACTICIPANT="GoAdminService"
PACT_CLI="docker run --rm -v ${PWD}:${PWD} -e PACT_BROKER_BASE_URL -e PACT_BROKER_TOKEN  pactfoundation/pact-cli"

deps:
	@echo "--- 🐿  Fetching build dependencies "
	cd /tmp; \
	go install github.com/mitchellh/gox@latest; \
	cd -

install:
	@if [ ! -d pact/bin ]; then\
		echo "--- 🐿 Installing Pact CLI dependencies"; \
		curl -fsSL https://raw.githubusercontent.com/pact-foundation/pact-ruby-standalone/master/install.sh | bash -x; \
  	fi

run-consumer:
	@go run client/cmd/main.go

deploy-consumer:
	@echo "--- ✅ Checking if we can deploy consumer"
	@"${PACT_CLI}" broker can-i-deploy \
		--pacticipant $(PACTICIPANT) \
		--broker-base-url ${PACT_BROKER_PROTO}://$(PACT_BROKER_URL) \
		--broker-token ${PACT_BROKER_TOKEN} \
		--latest

deploy: deploy_app record_deployment

deploy_app:
	@echo "\n========== STAGE: deploy ==========\n"
	@echo "Deploying to production"

record_deployment:
	@"${PACT_CLI}" record-deployment \
	 --pacticipant ${PACTICIPANT} \
	 --version ${GIT_COMMIT} \
	 --broker-base-url ${PACT_BROKER_PROTO}://$(PACT_BROKER_URL) \
	 --broker-token ${PACT_BROKER_TOKEN} \
	 --environment production

publish:
	@echo "--- 📝 Publishing Pacts"
	go run client/pact/publish.go
	@echo
	@echo "Pact contract publishing complete!"
	@echo
	@echo "Head over to $(PACT_BROKER_PROTO)://$(PACT_BROKER_URL) and login"
	@echo "to see your published contracts.	"

unit:
	@echo "--- 🔨Running Unit tests "
	go test -tags=unit -count=1 ${PWD}/client -run 'TestClientUnit'

consumer: export PACT_TEST := true
consumer: 
	@echo "--- 🔨Running Consumer Pact tests "
	go test -tags=integration -count=1 ${PWD}/client -run 'TestClientPact'
