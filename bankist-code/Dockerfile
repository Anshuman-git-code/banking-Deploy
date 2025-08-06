# Use the official Nginx image as base
FROM nginx:alpine

# Copy the static files to Nginx's default serving directory
COPY . /usr/share/nginx/html/

# Copy a custom Nginx configuration (optional)
# COPY nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"] 