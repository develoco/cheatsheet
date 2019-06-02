javascript, test, tdd, mocha, chai, jasmine

# Testing with mocha and chai

Install

    npm install mocha chai

Write

    /* eslint-env mocha */
    const assert = require('chai').assert

    describe('On the live chat...', () => {
      ...

      it('when entering the live chat I see an indicator of messages being loaded', () => {
        ...
        assert.isNull(store.getState().activeConversation, null)
        assert.isTrue(store.getState().messagesLoading)
        assert.isFalse(store.getState().messagesLoading)
        assert.deepEqual(store.getState().messages, messages)
        assert.include(store.getState().messages, message)
        assert.notInclude(store.getState().messages, message)
        assert.isNotNull(store.getState().conversations)
        assert.equal(store.getState().chat.activeConversation, 1)
      })

Run tests

       mocha spec
