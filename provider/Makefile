PACTICIPANT="GoUserService"
PACT_CLI="docker run --rm -v ${PWD}:${PWD} -e PACT_BROKER_BASE_URL -e PACT_BROKER_TOKEN  pactfoundation/pact-cli"

install:
	@if [ ! -d pact/bin ]; then\
		echo "--- Installing Pact CLI dependencies";\
		curl -fsSL https://raw.githubusercontent.com/pact-foundation/pact-ruby-standalone/master/install.sh | bash;\
    fi

run-provider:
	@go run ${PWD}/cmd/usersvc/main.go

deploy-provider:
	@echo "--- ✅ Checking if we can deploy provider"
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
	@"${PACT_CLI}" broker record-deployment --pacticipant ${PACTICIPANT} --version 1.0.0 --environment production


provider: export PACT_TEST := true
provider:
	@echo "--- 🔨Running Provider Pact tests "
	go test -count=1 -tags=integration ${PWD} -run "TestPactProvider"

.PHONY: install deploy-consumer deploy-provider publish unit consumer provider
