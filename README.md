# ngx-trpc

TRPC Client for Angular.

Dependencies:

- pnpm `pnpm install trpc-client ngx-trpc`
- npm `npm install trpc-client ngx-trpc`

Project setup:

```typescript
import {inject, InjectionToken, Provider} from '@angular/core';

import {httpBatchLink} from '@trpc/client';
import {createTRPCRxJSProxyClient} from 'ngx-trpc';

import type {AppRouter} from './your-backend';

export const TRPC_PROVIDER = new InjectionToken<ReturnType<typeof createTRPCRxJSProxyClient<AppRouter>>>('___TRPC_PROVIDER___');
export const provideTRPCClient = (): Provider => ({
  provide: TRPC_PROVIDER,
  useFactory: () =>
    createTRPCRxJSProxyClient<AppRouter>({
      links: [
        httpBatchLink({
          url: 'http://backend-url/trpc',
        }),
      ],
    }),
});

export const injectTRPCClient = (): ReturnType<typeof createTRPCRxJSProxyClient<AppRouter>> => inject(TRPC_PROVIDER);
```

Example usage:

```typescript
@Component({
  selector: 'app-post-list',
  standalone: true,
  template: `
    <h1>Posts</h1>
    <div *ngIf="posts$ | async as posts">
      <article *ngFor="let post of posts">
        <h3>{{ post.title }}</h3>
      </article>
    </div>

    <hr />

    <form #f="ngForm" (ngSubmit)="submitForm(f)">
      <label for="title">Title:</label>
      <br />
      <input ngModel id="title" name="title" type="text" />

      <br />
      <label for="text">Text:</label>
      <br />
      <textarea ngModel id="text" name="text"></textarea>
      <br />
      <button type="submit">Submit</button>
    </form>
  `,
})
export class PostListComponent {
  trpcClient = injectTRPCClient();

  posts$ = this.trpcClient.post.list.query().pipe(tap((posts) => console.log('Posts', posts)));

  submitForm(form: NgForm) {
    const input = {
      title: form.form.controls['title'].getRawValue() as string,
      text: form.form.controls['text'].getRawValue() as string,
    };

    this.trpcClient.post.add.mutate(input).subscribe((res) => console.log(res));
  }
}
```

Libs used:

- TRPC https://trpc.io
- RxJS https://rxjs.dev

## Commands

### Build

```shell
pnpn exec build ngx-trpc
```
