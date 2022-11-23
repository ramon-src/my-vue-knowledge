## Mocking APIs

Create a folder named \_\_mocks\_\_ in the same level of the file that you want to mock

- store/
  - permissions.js
- services/
  - api.js
  - \_\_mocks\_\_/
    - api.js


The original permissions store

```js
// store/permissions.js
import api from "@/services/api";

export default {
  namespaced: true,

  state: {
    permissions: [],
  },

  mutations: {
    setPermissions(state, permissions) {
      state.permissions = permissions
    },
  },

  actions: {
    async fetchPermissions({ commit }) {
      try {
        const { data } = await api.permissions.get();
        commit("setPermissions", data);
      } catch ($e) {
        throw new Error("Something went wrong");
      }
    },
  },
};
```

The original api service

```js
// services/api.js

export default {
  users: { findById: (id) => axios.get(`/users/${id}`) },
  permissions: { get: () => axios.get("/permissions") },
};
```

The mock api file

```js
// services/__mocks__/api.js

let data;

export const mockApiResponse = (newData) => {
  data = newData;
};

export default {
  users: { findById: jest.fn(() => Promise.resolve({ data })) },
  permissions: { get: jest.fn(() => Promise.resolve({ data })) },
};
```

Inside a store or component test that needs to mock api results to work correctly

```js
jest.mock("@/services/api"); // overwrite the original api service

import { mockApiResponse } from "@/services/api"; // so now we can use the new function of the mock

describe("Permissions store", () => {
  describe("actions", () => {
    it("should fetchPermissions and commit the data", async () => {
      const commit = jest.fn();
      const response = ["USER_EDIT", "USER_DELETE"];
      mockApiResponse(response);

      await permissionStore.actions.fetchPermissions({ commit });

      expect(commit).toHaveBeenCalledWith("setPermissions", response);
    });
  });
});
```
