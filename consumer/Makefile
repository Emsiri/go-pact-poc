PACTICIPANT="GoUserService"

install:
	@if [ ! -d pact/bin ]; then\
		echo "--- Installing Pact CLI dependencies";\
		curl -fsSL https://raw.githubusercontent.com/pact-foundation/pact-ruby-standalone/master/install.sh | bash;\
    fi

run-consumer:
	@go run client/cmd/main.go

deploy-consumer: install
	@echo "--- ✅ Checking if we can deploy consumer"
	@pact-broker can-i-deploy \
		--pacticipant $(CONSUMER_NAME) \
		--broker-base-url ${PACT_BROKER_PROTO}://$(PACT_BROKER_URL) \
		--broker-token ${PACT_BROKER_TOKEN} \
		--latest

deploy: deploy_app record_deployment

deploy_app:
	@echo "\n========== STAGE: deploy ==========\n"
	@echo "Deploying to production"

record_deployment:
	@pact-broker record-deployment \
	 --pacticipant ${PACTICIPANT} \
	 --version ${GIT_COMMIT} \
	 --broker-base-url ${PACT_BROKER_PROTO}://$(PACT_BROKER_URL) \
	 --broker-token ${PACT_BROKER_TOKEN} \
	 --environment production

publish: install
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
consumer: install
	@echo "--- 🔨Running Consumer Pact tests "
	go test -tags=integration -count=1 ${PWD}/client -run 'TestClientPact'
